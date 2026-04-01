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

# Analysis in Practice

## Descriptive Analysis
- **Descriptive analysis** is an essential step in any data analysis. It serves to describe a data set based on individual characteristics. It helps to detect possible errors in data collection or outliers in the data set.

1. What is the issue?
    * Suspected breach? Networking issue?
2. Define our scope and the goal. (what are we looking for? which time period?)
    * Target: multiple hosts potentially downloading a malicious file from bad.example.com
    * When: within the last 48 hours + 2 hours from now.
    * Supporting info: filenames/types 'superbad.exe' 'new-crypto-miner.exe'
3. Define our target(s) (net / host(s) / protocol)
    * Scope: 192.168.100.0/24 network, protocols used were HTTP and FTP.
- Using our workflow, we will determine our issue, what we are looking for, when, and where to find it. Descriptive analysis covers these critical concepts for our analysis.

## Diagnostic Analysis
- Diagnostic analysis clarifies the causes, effects, and interactions of conditions. In doing so, it provides insights that are obtained through correlations and interpretation.

4. Capture network traffic
    * Plug into a link with access to the 192.168.100.0/24 network to capture live traffic to try and grab one of the executables in transfer. See if an admin can pull PCAP or netflow data from our SIEM for the historical data.
5. Identification of required network traffic components (filtering)
    * Once we have traffic, filter out any packets not needed for this investigation to include; any traffic that matches our common baseline and keep anything relevant to the scope of the investigation. For example, HTTP and FTP from the subnet, anything transferring or containing a GET request for the suspected executable files.
6. An understanding of captured network traffic
    * Once we have filtered out the noise, it is time to dig for our targets—filter on things like ftp-data to find any files transferred and reconstruct them. For HTTP, we can filter on http.request.method == "GET" to see any GET requests that match the filenames we are searching for. This can show us who has acquired the files and potentially other transfers internal to the network on the same protocols.
- By capturing traffic around the source of our issue, clearing out any known good data, and then taking the time to inspect and understand what is left, we can determine if it is the cause of our problem. In doing so, we just performed diagnostic analysis. We are validating the cause of our problems and examining the events surrounding them.

## Predictive Analysis
- By evaluating historical and current data, predictive analysis creates a predictive model for future probabilities.
- Based on the results of descriptive and diagnostic analyses, this method of data analysis makes it possible to identify trends, detect deviations from expected values at an early stage, and predict future occurrences as accurately as possible.

7. Note-taking and mind mapping of the found results
    * Annotating everything we do, see, or find throughout the investigation is crucial. Ensure we are taking ample notes, including:
        * Timeframes we captured traffic during.
        * Suspicious hosts within the network.
        * Conversations containing the files in question. ( to include timestamps and packet numbers)
8. Summary of the analysis (what did we find?)
    * Finally, summarize what we have found explaining the relevant details so that superiors can decide to quarantine the affected hosts or perform more significant incident response.
    * Our analysis will affect decisions made, so it is essential to be as clear and concise as possible.
- By performing an evaluation of the data we have found, comparing it to our baseline traffic, and known bad data such as markers of infiltration or exploitation (like signatures for viruses and other hacking tools), we are performing Predictive Analysis. In this process, we paint a clear picture so that appropriate actions can be taken in response.

## Prescriptive Analysis
- Prescriptive analysis aims to narrow down what actions to take to eliminate or prevent a future problem or trigger a specific activity or process. - Using the results of our workflow, we can make sound decisions as to what actions are required to solve the problem and prevent it from happening again.
- This workflow is an example of how to begin the analysis process on captured traffic. Above we broke it down into its parts to explain where they fit within the analysis process and with which type of analysis it belongs. We include it here again as a whole so that it can serve as a template.

1. What is the issue?
    * Suspected breach? Networking issue?
2. Define our scope and the goal (what are we looking for? which time period?)
    * target: multiple hosts potentially downloading a malicious file from bad.example.com
    * when: within the last 48 hours + 2 hours from now.
    * supporting info: filenames/types 'superbad.exe' 'new-crypto-miner.exe'
3. Define our target(s) (net / host(s) / protocol)
    * scope: 192.168.100.0/24 network protocols used were HTTP and FTP.
4. Capture network traffic
    * plug into a link with access to the 192.168.100.0/24 network to capture live traffic to try and grab one of the executables in transfer. See if an admin can pull PCAP and/or netflow data from our SIEM for the historical data.
5. Identification of required network traffic components (filtering)
    * once we have traffic, filter out any traffic not needed for this investigation to include; any traffic that matches our common baseline and keep anything relevant to the scope. `HTTP and FTP from the subnet, anything transferring or containing a GET request for the suspected executable files.
6. An understanding of captured network traffic
    * Once we have filtered out the noise, it's time to dig for our targets—filter on things like ftp-data to find any files transferred and reconstruct them. For HTTP, we can filter on http.request.method == "GET" to see any GET requests that match the filenames we are searching for. This can show us who has acquired the files and potential other transfers internal to the network on the same protocols.
7. Note-taking and mind mapping of the found results.
    * Annotating everything we do, see, or find throughout the investigation is crucial. Ensure we are taking ample notes, including:
        * Timeframes we captured traffic during.
        * Suspicious hosts within the network.
        * Conversations containing the files in question. ( to include timestamps and packet numbers)
8. Summary of the analysis (what did we find?)
    * Finally, summarize what has been found, explaining the relevant details so that superiors can make an informed decision to quarantine the affected hosts or perform more significant incident response.
    * Our analysis will affect decisions made, so it is essential to be as clear and concise as possible.

## Key Components of an Effective Analysis
1. Know your environment
- If we are unsure if a host belongs in the network, how can we determine if it is rogue or not? Keeping asset inventories and network maps is vital. These will aid in the analysis process.
2. Placement is Key
- Closest to the source of the issue is the ideal placement of our capturing tool. If the traffic in question is coming from the internet, listening to the inbound links is a great way to see the complete picture. It is as close to the source as we, the administrators, can get. If the problem seems to be isolated to one host on our internal network, try placing the capture tools in the same segment as the problem host and see what traffic is happening within the segment.
3. Persistence
- The issue will not always be easy to spot. It may not even be a frequent event on the network. For example, an attacker's Command and Control server reaching out to the victim's computers may only happen on a time interval of once every several hours, or even once a day or less. This means that if we did not catch it the first time around, it might be a while before it appears in our logs.

## Analysis Approach
1. Start with standard protocols first and work our way into the austere and specific only to the organization. 
2. Look for patterns. Is a specific host or set of hosts checking in with something on the internet at the same time daily?
3. Check anything host to host within our network. In a standard setup, the user's hosts will rarely talk to each other. So be suspicious of any traffic that appears like this.
4. Look for unique events. 
5. Don't be afraid to ask for help.