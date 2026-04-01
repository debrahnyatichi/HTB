# The Analysis Process
- Network Traffic Analysis is a dynamic process that can change depending on the tools we have on hand, permissions given to us by the organization, and our network's visibility. 
- Our goal is to provide a repeatable process we can begin to utilize when performing traffic analysis.
- **Traffic Analysis** is a detailed examination of an event or process, determining its origin and impact, which can be used to trigger specific precautions or actions to support or prevent future occurrences.
- Traffic analysis is a highly versatile and essential tool to have in our defensive toolbox.

## Analysis Dependencies
- Traffic capturing and analysis can be performed in two different ways, **active** or **passive**.
- With passive, we are just copying data that we can see without directly interacting with the packets.
- For active traffic capture and analysis, it requires us to take a more hands-on approach. This process can also be referred to as **in-line** traffic captures.
- With both, how we analyze the data is up to us. We can perform the capture and analysis once done, or we can perform analysis in real-time while the traffic is live.
- The table below lays out the dependencies for each.

### Traffic Capture Dependencies
| Dependency | Passive | Active | Description |
|------------|:-------:|:------:|-------------|
| Permission | ✅ | ✅ | Depending on the organization we are working in, capturing data can be against policy or even against the law in some sensitive areas like healthcare or banking. Be sure always to obtain permission in writing from someone with the proper authority to grant it to you. We may style ourselves as hackers, but we want to stay in the light legally and ethically. |
| Mirrored Port | ✅ | ☐ | A switch or router network interface configured to copy data from other sources to that specific interface, along with the capability to place your NIC into promiscuous mode. Having packets copied to our port allows us to inspect any traffic destined to the other links we could normally not have visibility over. Since VLANs and switch ports will not forward traffic outside of their broadcast domain, we have to be connected to the segment or have that traffic copied to our specific port. When dealing with wireless, passive can be a bit more complicated. We must be connected to the SSID we wish to capture traffic off of. Just passively listening to the airwaves around us will present us with many SSID broadcast advertisements, but not much else. |
| Capture Tool | ✅ | ✅ | A way to ingest the traffic. A computer with access to tools like TCPDump, Wireshark, Netminer, or others is sufficient. Keep in mind that when dealing with PCAP data, these files can get pretty large quickly. Each time we apply a filter to it in tools like Wireshark, it causes the application to parse that data again. This can be a resource-intensive process, so make sure the host has abundant resources. |
| In-line Placement | ☐ | ✅ | Placing a Tap in-line requires a topology change for the network you are working in. The source and destination hosts will not notice a difference in the traffic, but for the sake of routing and switching, it will be an invisible next hop the traffic passes through on its way to the destination. |
| Network Tap or Host With Multiple NIC's | ☐ | ✅ | A computer with two NIC's, or a device such as a Network Tap is required to allow the data we are inspecting to flow still. Think of it as adding another router in the middle of a link. To actively capture the traffic, we will be duplicating data directly from the sources. The best placement for a tap is in a layer three link between switched segments. It allows for the capture of any traffic routing outside of the local network. A switched port or VLAN segmentation does not filter our view here. |
| Storage and Processing Power | ✅ | ✅ | You will need plenty of storage space and processing power for traffic capture off a tap. Much more traffic is traversing a layer three link than just inside a switched LAN. Think of it like this: When we passively capture traffic inside a LAN, it's like pouring water into a cup from a water fountain — it's a steady stream but manageable. Actively grabbing traffic from a routed link is more like using a water hose to fill up a teacup. There is a lot more pressure behind the flow, and it can be a lot for the host to process and store. |

