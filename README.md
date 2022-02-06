# k8s-demo
These are some deomos for moving services to K8s, including cronjob service, a SpringBoot web service, a Dubbo service, and a web service that consumes the Dubbo service.

## Common processes
1. Enter a demo, e.g. `d dubbo-demo`
2. `mvn clean package`
3. Create Dockefile and run docker build
4. Run kubectl apply (yml files are in the config directory)

## Some notes
### Dubbo service
1. You need to install a ZooKeeper beforehand that will be used the service registry, then change ZooKeeper address in dubbo-demo/src/resources/dubbo.properties:
```
dubbo.registry.address=zookeeper://<zookeeper_host_address>:2181
```
2. Before running `mvn package` for the dubbo demo, 
```bash
cd dubbo-demo-api
mvn install
```
3. Notice tha we set `hostNetwork: true` in config/dubbo.yaml because otherwise the Dubb service will use its docker IP(172.17...) to register itself to the ZooKeeper service. As a result, other services outside the K8s cluster cannot consume the service. Setting `hostNetwork: true` enables the Dubbo service to register itself to the ZooKeeper service using the host IP. 
4. As a result of the hostNetwork configuration, the container port needs to be configured dynamically, otherwise on host can only have one Dubbo service. A workaround is to set an env variable in the yml file, and then modify start.sh to change the dubbo port in fly. See dubbo-demo/target/Root/bin/start.sh
5. You can verify that the Dubbo service is successfully deploy yo K8s by doing the follows:
```bash
telnet <host_ip_where_dubbo_service_is_running>:<container_port_defined_in_yml>
#see service list
ls
#invoke service
invoke com.mooc.demo.api.DemoService.sayHello("hh")
```