---
title: "[Networking] How Longest prefix matching effect routing"
datePublished: Mon Oct 16 2023 04:07:57 GMT+0000 (Coordinated Universal Time)
cuid: clnsdkcl800010amn6m617tck
slug: networking-how-longest-prefix-matching-effect-routing
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/7nrsVjvALnA/upload/6c11bb8e4a3e42f45f6f608448dc918c.jpeg
tags: network, routing, networking

---

# When will two routes considered duplicated?

Route duplication is a situation where **two or more routes to the same destination exist in a routing table**.

This can happen for several reasons, such as misconfiguration, or the use of overlapping prefixes in different networks.

In some cases, it may be necessary to have two routes to the same destination, such as for load balancing or redundancy. However, in most cases, having duplicate routes is not necessary and can lead to problems.

Route duplication can cause many problems, including routing loops, black holes, and performance problems. It is important to detect and avoid route duplication to ensure the smooth operation of your network.

# Longest prefix matching

Longest prefix matching (LPM) is a fundamental concept in networking that plays a critical role in IP routing. It allows for the efficient forwarding of packets, enables complex network policies, and is essential for the operation of the Internet.

In packet routing, there is a possibility of overlapping entries in the routing table. As a result, we must pick an IP prefix that is more specific to the destination address.

For example, suppose you want to send a letter to City A and District B and there are two possible mailmen to choose from:

* The first mailman can hand over his letter to your destination, who works in a post office in city A. He has to search the hold city to find the receiver.
    
* Another mailman works in a particular district B of that city. He lives in the same neighborhood and knows exactly where to find the receiver.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1695788083668/f6a7d4de-697e-499d-9853-6fb6bacbc5ca.png align="center")

As a result, giving the letter to the second person is more efficient because he handles more localized regions.

LPM works in the same way. By comparing the destination IP address of a packet to the prefixes in a routing table, the router selects the route with **the longest prefix or a more specific route** that matches the destination IP address.

## Duplicate routes

For example, consider the following routing table:

| **Prefix** | Destination | **Next hop** |
| --- | --- | --- |
| 192.168.1.0/24 | 192.168.1.100 | 192.168.1.1 |
| 192.168.0.0/16 | 192.168.1.100 | 192.168.0.1 |

If the router receives a packet with the destination IP address 192.168.1.100, it will use the first route in the routing table, because it has **the longest prefix that matches the destination IP address.**

The second route would also match the destination IP address, but it is less specific because it has a shorter prefix.

The longest prefix rule is used by all IP routers, regardless of the routing protocol that they are using. It is also used in other networking technologies, such as MPLS and BGP.

## Overlapping subnets

The longest prefix rule can also be used to handle overlapping subnets. For example, consider the following subnets:

* 192.168.1.0/23
    
* 192.168.1.128/24
    

In this case, the two subnets overlap because the IP addresses 192.168.1.128 to 192.168.1.255 belong to both subnets. If a router on this network receives a packet with the destination IP address 192.168.1.130, it will use the longest prefix rule to determine which subnet to forward the packet to.

In this case, the longest prefix that matches the destination IP address is 192.168.1.128/24. Therefore, the router will forward the packet to the 192.168.1.128/24 subnet.

# Undesirable routing with LPM

When you already have a route to a specific host and a new route is received with a longer prefix, the new route will take precedence. This is because LPM always chooses the most specific route to forward a packet.

For example, consider the following scenario:

* You have a route with the prefix 192.168.1.0/24, and next-hop to 192.168.1.24
    
* You receive a new route with the prefix 192.168.1.100/32, and next-hop to 192.168.1.28
    

In this scenario, the new route with the prefix 192.168.1.100/32 will take precedence. This means that any packets destined for the host 192.168.1.100 will be forwarded to 192.168.1.28 even though you already have a route to the host 192.168.1.100 with the next-hop 192.168.1.24.

In real-world scenarios, network engineers often want a specific path to direct the traffic to and don't want the traffic to go to a new route. In this case, LPM behavior can be undesirable.

# Control LPM behavior

To prevent the router from using a new route with a longer prefix, you can use a routing policy. A routing policy is a set of rules that control how the router selects and forwards packets.

Here is an example of a route-map that prefers static routes over routes learned from routing protocols on Cisco syntax:

```bash
route-map PREFERED_ROUTES
  match ip address prefix-list PREFERED_ROUTES_LIST
  set local-preference 100
!
interface Ethernet0/0
  ip route-policy PREFERED_ROUTES in
end
```

This routing policy will match all packets that are destined for prefixes that are defined in the `prefix-list PREFERED_ROUTES_LIST`. For all matching packets, the routing policy will set the local preference to 100.

The local preference is a metric that is used by the router to select between multiple routes. A higher local preference indicates a more preferred route. Therefore, this routing policy will ensure that the router will always prefer static routes over routes learned from routing protocols.

This is just a simple example of manipulating the effect of LPM. You can use this to your advantage to create more complex routing policies.

It is important to note that if you have multiple routing policies configured on an interface, the router will apply the first routing policy that matches the packet.

# Advantages and disadvantages of LPM

LPM helps prevent duplicate routes in the routing table of a router. This is because LPM will always choose the most specific route to forward a packet. This comes with several benefits:

* **Improved performance:** By using the most specific route, the router can forward packets more quickly and efficiently.
    
* **Increased reliability:** The longest prefix rule helps to reduce the chance of routing loops and other problems.
    
* **Scalability:** The longest prefix rule can be used to scale networks of any size.
    

Despite that, there are a few potential negatives to using LPM:

* **Increased complexity:** LPM can be more complex to implement than other routing algorithms, such as static routing. This is because LPM requires the router to maintain a trie of all of the prefixes in its routing table.
    
* **Increased overhead:** The router must perform a depth-first search of the trie to find the longest prefix that matches the destination IP address of the packet.
    
* **Potential for routing loops:** This can happen if there are two or more routes in the routing table that have the same prefix length and the next hop addresses for those routes are different.
    

# Conclusion

The longest prefix rule is an essential part of IP networking, and it is one of the key reasons why the Internet is able to work so well.

Overall, LPM is a powerful and efficient routing algorithm that offers a number of benefits. However, it is important to be aware of the behavior of LPM and take steps to mitigate any undesirable behavior.