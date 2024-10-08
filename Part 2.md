# Google Cloud Networking: A Comprehensive Overview

## General Notes

### Google's Unique Network Approach:
Most internet providers send data through the network point closest to where it's going. Google does it differently - they route data through the network point closest to where it's coming from. This is a big deal and sets Google apart from others.

### Global Load Balancing Made Easy:
Google lets you use one special internet address (IP) to spread out traffic all over the world. This avoids a lot of the usual headaches with internet addressing and name systems.

### Traffic Costs:
Data coming into Google's network is free. Data going out usually costs money, but sometimes it's free when it's just moving between Google services.

### Google's Worldwide Network:
Google has connection points all over the world. These help with fast content delivery and connecting to their network. All of Google's data centers are connected by their own private global network.

### Flexible Networks:
In Google Cloud, networks span the whole world. Public internet addresses aren't tied to specific computers - Google's smart network system manages this flexibly.

### Subnets Across Zones:
Subnets (smaller network divisions) can stretch across different zones. This means computers in different zones but on the same subnet can talk to each other easily and share the same security rules.

### Reserved IP Addresses:
In each subnet, Google keeps four IP addresses for its own use. These are typically the first two and last two addresses in the range.

### Network Design Tips:
- Private network addresses shouldn't overlap when connecting different networks.
- It's best to give network access at the subnet level.
- Create separate networks for different teams, with shared services in a common network.
- It's better to use fewer, larger subnets than many small ones.

## Google's Unique Network Architecture

Google Cloud Platform (GCP) stands out in the cloud industry due to its innovative network design:

1. **Edge-Optimized Routing**: Unlike traditional networks that route traffic to the edge closest to the destination, Google routes traffic to enter at the edge nearest to the source. This approach significantly reduces latency and improves user experience.

   Example: If a user in New York accesses a GCP-hosted application with servers in London, the traffic enters Google's network at a point of presence (PoP) in New York, then travels on Google's private global network to London.

2. **Global Load Balancing**: GCP offers a single global anycast IP address for worldwide load balancing. This feature simplifies DNS management and improves reliability.

   Example: A global e-commerce platform can use a single IP address for its website, automatically routing users to the nearest available server, whether they're in Tokyo, São Paulo, or Berlin.

3. **Software-Defined Networking (SDN)**: Public IP addresses are decoupled from VMs and managed via SDN, allowing for flexible IP management and enhanced security.

   Example: You can easily move a public IP from one VM to another without changing DNS records, useful for maintenance or failover scenarios.

## VPC Firewall: Your Cloud Security Guard
Think of VPC Firewall rules as security guards for your cloud network. Here's how they work:
- **Global Protection:** These guards watch over your entire VPC, not just specific areas.
- **Customizable Security:** You can tell these guards to allow or block traffic based on where it's coming from, where it's going, and what kind of traffic it is.
- **Default Behavior:** By default, these guards are strict about what comes in (inbound traffic) but relaxed about what goes out (outbound traffic).
- **Rule Components:** Each firewall rule is like a set of instructions for the guard:
  - What to do (allow or block)
  - Which direction to watch (incoming or outgoing)
  - What kind of traffic to look for (like web traffic or email)
  - Who or what to apply the rule to
- **Priority System:** If guards get conflicting instructions, they follow the most important one first. Lower numbers mean higher importance.
- **Special Tags:** You can give your cloud resources special name tags, and the guards will treat them differently based on these tags.
Invisible Default Rules: There are some basic rules that always apply, even if you can't see them. But you can override these with your own, more important rules.
- **Safety First:** If there's any doubt, the guards will block traffic to be on the safe side.


## VPC Routing: Your Cloud Traffic Director
VPC Routing is like a smart traffic system for your cloud data. Here's how it works:
- **Global Traffic Control:** The routing rules apply everywhere in your VPC, not just in specific areas.
- **Next Step Guidance:** Routing doesn't plan the entire journey for your data; it just tells it where to go next, like signs at each intersection.
- **Address-Based Directions:** Routes tell your network where to send data based on its destination address.
- **Two-Part Permission:** For data to travel, it needs both a route (directions) and firewall permission (security clearance).
- **Internet Access Rules:** Currently, to send data to the internet, it must come from a resource with a public IP address, or use special services like Cloud NAT.
- **Flexible Control:** You can apply different routing rules to different resources using tags, giving you fine-grained control over your network traffic.

## VPC and Subnet Design

- **Global Networks**: VPC networks span all available regions, allowing for unified management of resources across the globe.
- **Cross-Zone Subnets**: Subnets can span multiple zones within a region, simplifying network design and improving fault tolerance.

Example: A subnet with range 10.0.0.0/16 can have VMs in us-central1-a, us-central1-b, and us-central1-c, all communicating as if they were on the same local network.

### VPC Firewall Rules

GCP's firewall rules offer granular control over network traffic:

- Rules are applied at the network level but can target specific instances using tags or service accounts.
- Implied rules exist by default, allowing outbound traffic and denying inbound traffic.

Example: You can create a rule allowing inbound traffic on port 80 to all instances with the tag "web-server", regardless of their IP addresses or subnets.

Certainly! Let's break down Google Cloud's external networking products and explain them with examples:

1. Domain Registration Services:

   a) Google Domains:
   - Aimed at consumers and small businesses
   - Example: A small bakery, "SweetTreats," can register sweettreats.com through Google Domains, getting privacy protection and easy DNS management.

   b) Cloud Domains:
   - Enterprise-focused, integrates with other Google Cloud services
   - Example: A large corporation, "MegaCorp," can manage multiple domains across its subsidiaries through Cloud Domains, with centralized billing and access control.

2. Cloud DNS:
   - Scalable and reliable DNS service
   - Example: An e-commerce site, "FastShop," uses Cloud DNS to ensure its website domain resolves quickly worldwide, handling millions of DNS queries during sales events.

3. Static IP:
   - Reserved IP addresses for GCP resources
   - Example: A financial services app, "MoneyMate," reserves a static IP for its main API server, ensuring clients can always reach it at the same address, even if the underlying infrastructure changes.

4. Load Balancing:
   - Distributes traffic across multiple instances
   - High-performance, scalable traffic distribution integrated with autoscaling and Cloud CDN. It’s built into the SDN and can handle spikes without pre-warming, no instances or devices. Can perform health checks and session affinity.
   - Global Load Balancers allow for multi-region failover for HTTP/S, SSL Proxy, and TCP Proxy. Prioritize low-latency connection to the region nearest to the user, then gently fail over in bits. Can react really quickly (unlike DNS) to changes in users, traffic, network, health, etc.
   - Example: A popular news website, "GlobalNews," uses Global Load Balancing to distribute traffic across servers in Asia, Europe, and America, ensuring readers get content from the nearest server.

5. Cloud CDN (Content Delivery Network):
   - Caches content close to users for faster delivery
   - Low-latency CDN based on HTTPS CLB that’s integrated with GCE and GCS. Supports HTTP/2 and HTTPS, but it can only get its data from within GCP. Simple checkbox on HTTPS load balancer. On a cache miss, pay origin POP “cache fill” egress charge, then you pay egress from POP and cache invalidation.
   - Example: A video streaming service, "StreamFlix," uses Cloud CDN to cache popular movies at edge locations worldwide, reducing buffering for users and load on origin servers.

6. Cache Management:
   - Customizable caching strategies
   - Example: An online store, "FashionForward," customizes its CDN cache keys to exclude user-specific query parameters, improving cache hit rates for product pages while still allowing personalized content through JavaScript.

These services work together to create a robust, scalable, and efficient network infrastructure. For instance, "GlobalTech," a multinational tech company, might use:
- Cloud Domains to manage its various brand domains
- Cloud DNS for reliable name resolution
- Global Load Balancing to distribute traffic across its worldwide data centers
- Cloud CDN to speed up content delivery
- Static IPs for its critical services

This setup ensures GlobalTech's services are fast, reliable, and can handle global traffic efficiently.

## Internal Networking Products
### VPC Connectivity Options:
- Cloud Interconnect: Provides private, high-bandwidth connections to your VPC networks.
  - Dedicated Interconnect: Direct physical links for high-volume data transfer (up to 200 Gbps in some regions).
  - Partner Interconnect: Similar capabilities through service provider partners.
  
- Cloud VPN: Secure, encrypted tunnels over the public internet for lower-volume connections.
  - HA VPN: The recommended VPN solution, offering high availability.
Public Google Services Connectivity:
- External Peering: Optimized connections to public Google services (no SLAs).
    - Direct Peering: For high-volume needs, requires meeting Google's peering requirements.
- Carrier Peering: Lower-volume option through partner providers.

#### Additional Networking Features:
- CDN Interconnect: Direct connections to supported CDN providers.
- Private Google Access: Access Google services without internet egress.
- Private Service Connect: Secure service consumption across VPC networks.
- Private Services Access: Allows third-party services to offer internal IPs in your VPC.
- Cloud Router: Dynamic routing solution using BGP.
- Cloud NAT: Managed outbound internet access for instances without public IPs.
- Identity-Aware Proxy (IAP): Application-level access control, can replace VPN in some cases.
- VPC Service Controls: Data exfiltration prevention and access control for GCP services.
- VPC Flow Logs: Network traffic logging for monitoring, security, and optimization.

#### Key Considerations:
- Interconnect solutions provide VPC connectivity, while Peering options are for public Google service access.
- Choose between direct connections or partner-mediated options based on your requirements and capabilities.
- Consider factors like bandwidth needs, security requirements, and supported GCP services when selecting a solution.
- Ensure compliance with GCP's requirements for direct peering and other connectivity options.
- Evaluate the costs associated with each option, including bandwidth charges and any additional fees.
- Plan for redundancy and high availability in your network architecture.
- Consider integrating with other GCP services, such as Cloud CDN, for a comprehensive solution.
- Regularly review and update your network architecture to ensure it aligns with your evolving business needs.

## External Load Balancers

This [video](https://www.youtube.com/watch?v=HUHBq_VGgFg) is a good overview of choosing a Cloud Load Balancer.

### Network Load Balancers

Operating at Layer 3 and Layer 4, external load balancers that face the internet. They are also regional load balancers, they operate within a region.

- Regional = HA with multiple zones
- Don’t look at Layer 7, just for TCP and UDP traffic
- Client IP is preserved
- Client access can be controlled with VPC firewall because client IP is preserved
- Balances traffic using 2-, 3-, or 5-tuple hashing (Source IP, Dest IP, Src/Dest Port, Protocol)
- Maintains session affinity based on the IP address
- Supports HTTP health checks on the back ends
- High performance, can handle 1,000,000+ requests per second

### We don’t use DNS-Based Global Load Balancing because:
- Delay in state convergence, caching records
- Regional load balancers become chokepoints

### Google Global Load Balancer (Layer 7)

- Uses a single global anycast virtual IP (VIP) with a single DNS record
- We built this because we needed to to make Google function for a billion + users
- CDN lays parallel to this, customers hit global LB first, then gets sent to CDN
- Some customers stick this on the edge, then use their own hardware as backends
- Built in HA running on Google’s edge, highly scalable
- Handles websocket traffic natively
- GFE is Google Front End, internal terminology for this product

![alt text](<images/Global HTTP(S) Load Balancing Data Model.png>)
- Backends are either Instance Groups or Network Endpoint Groups. Use Network Endpoint Groups with VPC-Native GKE Clusters for more integrated K8s load balancing. More here.
- Client IP is not passed through, it is terminated at the load balancer and opens a new session on the backside using private IPs
- Cloud Armor works in tandem with this if you want to allow/deny specific IP blocks

Health checks determine which instances of a load balancer can receive new connections. For HTTP load balancing, the health check probes to your load balanced instances come from addresses in the ranges 130.211.0.0/22 and 35.191.0.0/16. Your firewall rules must allow these connections.

## Internal Load Balancers

There really is no load balancer, you’re really manipulating the SDN control plane, not adding a VM or box on top of the workload. It’s delivered on Andromeda, not a middle proxy. No single point of failure. Dynamically scales up and down as needed.

![alt text](<images/L4 Regional Load Balancer.png>)

### L4 Regional Load Balancer

- Internal (RFC 1918) VIP
- Client IP is preserved
- TCP, HTTP, HTTPS health checks
- Can scale compute engines based on Cloud Load Balancer metrics

## Additional Links
- [Google Cloud Networking Documentation](https://cloud.google.com/networking/docs)
- [GCP: Best Practices VPC Design](https://cloud.google.com/architecture/best-practices-vpc-design)
- [Moifying Projects](https://cloud.google.com/billing/docs/how-to/modify-project/)
- [Ingress Egress Rules GCP](https://cloud.google.com/vpc-service-controls/docs/ingress-egress-rules?authuser=4)
- [Using Committed Use Discounts](https://www.pythian.com/blog/technical-track/unlocking-cost-savings-how-to-take-advantage-of-google-cloud-committed-use-discounts)
- [Cloud Security: Ingress & Egress](https://aviatrix.com/learn-center/cloud-security/egress-and-ingress/)
- [GCP CDN Overview](https://cloud.google.com/cdn/docs/overview)
- [GCP DNS Overview](https://cloud.google.com/dns/docs/overview)
- [GCP Loadbalancing](https://cloud.google.com/load-balancing)
- [GCP Static IP Setup Guide](https://sudipta-deb.in/2022/11/how-do-i-setup-a-static-ip-with-google-cloud.html)
- [Google domains: How To](https://kinsta.com/blog/google-domains-review/)