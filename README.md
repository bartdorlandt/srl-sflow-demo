# srl-sflow-demo
Inspired by Peter Phaal's sFlow demo at https://blog.sflow.com/2021/04/containerlab.html this lab creates a leaf-spine CLOS topology using SR Linux containers, fully [configured](https://github.com/jbemmel/srl-sflow-demo/blob/main/leaf1.cfg.json#L1223) to send sFlow records to Peter's collector.

In SR Linux, sFlow records are sent inband (and not via the management network); the lab uses eBGP to have Spine1 advertise a 172.20.20.0/24 route to its ethernet-1/3 interface which is connected to the sFlow collector.

Note: The Containerlab config file uses a [beta](https://github.com/jbemmel/srl-sflow-demo/blob/main/srl-test.yml#L58) feature to connect spine1 e1-3 to the Linux management network bridge, for containerlab versions before 0.13 you could also use a separate manual bridge with routing as [shown](https://github.com/jbemmel/srl-sflow-demo/blob/main/srl-test.yml#L41) (commented out); this requires a static route to 172.20.20.0/24 on spine1

## Deploy

`sudo clab deploy -t ./srl-test.yml`

## sFlow web interface

Open a web browser to http://172.20.20.10:8008/html/index.html to see sFlow stats coming in.
For the lab, only leaf1 is configured to send sFlow UDP packets to the collector:
```
A:leaf1# /system sflow                                                                                                                                                                                             
--{ running }--[ system sflow ]--                                                                                                                                                                                  
A:leaf1# info                                                                                                                                                                                                      
    admin-state enable
    collector 1 {
        collector-address 172.20.20.10
        network-instance default
        source-address 192.168.0.1
    }
```
