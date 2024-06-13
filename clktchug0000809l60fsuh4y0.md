---
title: "The OSI Networking Model"
datePublished: Wed Aug 02 2023 06:26:39 GMT+0000 (Coordinated Universal Time)
cuid: clktchug0000809l60fsuh4y0
slug: the-osi-networking-model
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/40XgDxBfYXM/upload/3ba5559e00d8e587961c4ed437cdbed1.jpeg
tags: networking

---

# **What Is OSI?**

The Open Systems Interconnection (OSI) model is a conceptual model that describes how different communication systems talk to each other on a computer network

OSI was the first standard model for network communications, adopted by all major computer and telecommunication companies in the early 1980s.

The modern Internet is not based on OSI but on the simpler TCP/IP model. However, the OSI 7-layer model is still widely used, as it helps visualize and communicate how networks operate and helps isolate and troubleshoot networking problems.

# **OSI Layers**

OSI contains 7 layers.

These layers are the Application, Presentation, Session, Transport, Network, Data link, and Physical layer.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691976394211/6f88d899-a0f9-465f-af62-35cbe404c534.png align="center")

## **Application Layer**

The application layer is used by user-facing software such as web browsers or email clients.

This layer provides services that include: e-mail, transferring files, distributing results to the user, directory services, network resources, and so on.

It provides protocols that allow the software to send and receive information and present meaningful data to users.

A few examples of application layer protocols are the Hypertext Transfer Protocol (HTTP), File Transfer Protocol (FTP), Post Office Protocol (POP), Simple Mail Transfer Protocol (SMTP), Domain Name System (DNS), Teletype Network Protocol (Telnet),...

Depending on the information the user wants to send over the network, a specific protocol is used:

* The SMTP or POP3 protocol is used to send an e-mail message.
    
* The FTP protocol is used to transmit a file over the network.
    
* The Telnet protocol is used to control a remote device.
    

## **Presentation Layer**

The presentation layer prepares data for the application layer.

It ensures that the information that the application layer of one system is readable by the application layer of another system. It defines how two devices should encode, encrypt, and compress data so it is received correctly on the other end.

Simply put, the presentation layer serializes complex data structures into flat byte strings (using mechanisms such as TLV, XML, or JSON) before giving them to the application layer.

## **Session Layer**

The session layer creates communication channels between devices.

It is responsible for opening sessions, ensuring they remain open and functional while data is being transferred, and closing them when communication ends.

It also offers other services such as authentication and reconnection after an interruption. It creates checkpoints and recovery, which is useful for re-transmitting data in case of a system failure.

## **Transport Layer**

The transport layer is responsible for providing end-to-end communication between two devices.

On the sender end, the transport layer divides a message into smaller segments that contain a sequence number and the port address. Then it reassembles the segments on the receiving end, turning them back into data that can be used by the session layer.

The transport layer provides two types of services: connection-oriented and connectionless services.

* The Transmission Control Protocol (TCP) is a connection-oriented protocol that provides reliable data transfer. It establishes a connection between two devices before transmitting data. TCP provides error checking and flow control mechanisms to ensure that data is transmitted reliably.
    
* The User Datagram Protocol (UDP) is a connectionless protocol that provides fast data transfer. It does not establish a connection before transmitting data. UDP does not provide error checking or flow control mechanisms, so data may be lost or arrive out of order.
    

## **Network Layer**

The network layer is responsible for routing data packets from one network to another. It provides logical addressing and routing services.

It typically uses Internet Protocol (IP) addresses to route packets to a destination node.

It determines the best path for data transmission based on network conditions and traffic load.

The network layer also provides congestion control mechanisms to ensure that data is transmitted at an optimal rate.

## **Data Link Layer**

The data link layer is responsible for the transmission of data between devices on the same network. It breaks up packets into frames and sends them from source to destination.

This layer is composed of two parts:

* Logical Link Control (LLC), which identifies network protocols, performs error checking, and synchronizes frames,
    
* Media Access Control (MAC) uses MAC addresses to connect devices and define permissions to transmit and receive data.
    

## **Physical Layer**

The physical layer is responsible for the physical or wireless connection between network nodes.

It defines the connector, the electrical cable, or wireless technology connecting the devices, and is responsible for the transmission of the raw data, which is simply a series of 0s and 1s while taking care of bit rate control.

## **Advantages of the OSI model**

The advantages of the OSI model are:

* It helps to standardize routers, switches, motherboards, and other hardware.
    
* It is a generic model and acts as a guidance tool to develop any network model.
    
* It helps users and operators of computer networks to determine the required hardware and software to build their network.
    
* It helps users and operators of computer networks to understand and communicate the process followed by components communicating across a network.
    
* It helps users and operators perform troubleshooting, by identifying which network layer is causing an issue and focusing efforts on that layer.
    

## **OSI and TCP/IP**

TCP/IP and OSI are two different models used for network communication. OSI follows an academic approach, whereas TCP/IP follows a practical approach.

The TCP/IP model is a functional model designed to solve specific communication problems, while OSI is a generic, protocol-independent model intended to describe all forms of network communication.

TCP/IP is a standard protocol used for every network including the Internet, while OSI is not a protocol but a reference model used for understanding and designing the system architecture.

A key difference between the models is that TCP/IP is simpler, collapsing several OSI layers into one:

* OSI layers 5, 6, and 7 are combined into one application layer in TCP/IP.
    
* OSI layers 1, and 2 are combined into one network access layer in TCP/IP.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691976577187/f6f7ff00-9006-4304-bd51-9f25cf0989fb.png align="center")

Surprisingly, the TCP/IP model came into existence about 10 years before the OSI model. As it turned out, TCP/IP had too much momentum to be overtaken by the OSI model or any of the other competing network models.