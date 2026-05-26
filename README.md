# Azure Secure Network Lab — Portfolio-VNet

![Azure](https://img.shields.io/badge/Microsoft_Azure-0089D6?style=for-the-badge&logo=microsoft-azure&logoColor=white)
![Security](https://img.shields.io/badge/Network_Security-FF0000?style=for-the-badge&logo=shield&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)
![Level](https://img.shields.io/badge/Level-Beginner_to_Intermediate-blue?style=for-the-badge)

---

## Project Overview

This project demonstrates the design and deployment of a secure cloud network architecture on Microsoft Azure. The lab simulates a real-world two-tier application environment where a public-facing web layer and a private database layer are isolated from each other using network segmentation.

The core security principle applied throughout this lab is the principle of least privilege. No resource is given more network access than it strictly needs. The database layer is completely unreachable from the open internet and can only receive connections from the web layer through a single controlled port.

This lab was built entirely through the Azure Portal to demonstrate practical, hands-on cloud skills beyond certification theory.

---

## Architecture Diagram

[![Portfolio-VNet Architecture](architecture-diagram.svg)](https://github.com/hoqueariful/azure-secure-vnet-lab/blob/main/architecture-diagram.png)

---

## What Was Built

**Resource Group**
PortfolioVNet-RG was created as the top-level container for every resource in this lab. Grouping all resources together means they can be audited, managed, and deleted as a single unit with no leftover components or unexpected charges.

**Virtual Network**
Portfolio-VNet was deployed with an address space of 10.0.0.0/16, giving a total of 65,536 usable IP addresses that can be carved into smaller subnets as needed.

**Web-Subnet (10.0.1.0/24)**
This subnet represents the public-facing application tier. It is designed to host web servers or application servers that receive traffic directly from the internet. It accepts inbound HTTP traffic on port 80 and HTTPS traffic on port 443.

**DB-Subnet (10.0.2.0/24)**
This subnet represents the private backend tier. It is designed to host database servers that should never be exposed to the internet. It only accepts inbound SQL traffic on port 1433 and only when that traffic originates from within the Web-Subnet. All other inbound traffic is explicitly denied.

**Network Security Group**
Portfolio-NSG acts as the virtual firewall enforcing all traffic rules. It is associated with both subnets and contains four custom inbound security rules that control exactly what traffic is permitted to enter each layer.

---

## Network Security Group Rules

The following rules are configured in Portfolio-NSG in order of priority.

**Rule 1 — Allow-HTTP-Web**
Priority 100. Allows inbound TCP traffic on port 80 from any source to the Web-Subnet (10.0.1.0/24). This enables standard HTTP web browsing to the public application layer.

**Rule 2 — Allow-HTTPS-Web**
Priority 110. Allows inbound TCP traffic on port 443 from any source to the Web-Subnet (10.0.1.0/24). This enables encrypted HTTPS traffic to the public application layer.

**Rule 3 — Allow-DB-from-Web**
Priority 200. Allows inbound TCP traffic on port 1433 sourced exclusively from the Web-Subnet (10.0.1.0/24) to the DB-Subnet (10.0.2.0/24). This is the critical segmentation rule. No machine outside the web layer can communicate with the database.

**Rule 4 — Deny-All-DB**
Priority 300. Denies all inbound traffic from any source to the DB-Subnet (10.0.2.0/24). This acts as the final catch-all rule. Any traffic that did not match Rule 3 is dropped before it reaches the database subnet.

---

## Resource Summary

| Component | Name | Value |
|---|---|---|
| Resource Group | PortfolioVNet-RG | East US |
| Virtual Network | Portfolio-VNet | 10.0.0.0/16 |
| Web Subnet | Web-Subnet | 10.0.1.0/24 |
| DB Subnet | DB-Subnet | 10.0.2.0/24 |
| Network Security Group | Portfolio-NSG | East US |

---

## Security Concepts Demonstrated

**Network Segmentation**
The network is divided into two distinct tiers. Each tier has its own address space, its own security rules, and a clearly defined role. Segmentation limits the blast radius of a security incident. If the web layer is compromised, the attacker cannot reach the database layer directly.

**Principle of Least Privilege**
Every NSG rule grants only the minimum access required. The database does not accept HTTP traffic. The web layer does not accept SQL traffic from the internet. No subnet has blanket open access.

**Defense in Depth**
Security is enforced at the network level through the NSG rather than relying solely on application-level controls. Even if an application vulnerability were exploited, the NSG rules provide an independent layer of protection.

**Explicit Deny**
Rule 4 is an explicit deny rather than relying on Azure's default deny. This demonstrates awareness of security rule ordering and the importance of documenting intended denies rather than relying on implicit behavior.



## How to Reproduce This Lab

**Prerequisites**

A free Microsoft Azure account is all you need. New accounts receive USD 200 in free credits for 30 days. No credit card is charged during the free period if you stay within the free tier.

Sign up at portal.azure.com.

**Step 1 — Create Resource Group**

Navigate to Resource groups in the Azure Portal and create PortfolioVNet-RG in East US.

**Step 2 — Create Virtual Network**

Navigate to Virtual networks and create Portfolio-VNet. During the IP Addresses tab configuration, delete the default subnet and add Web-Subnet (10.0.1.0/24) and DB-Subnet (10.0.2.0/24) manually.

**Step 3 — Create NSG**

Navigate to Network security groups and create Portfolio-NSG in the same region and resource group.

**Step 4 — Add Inbound Rules**

Inside Portfolio-NSG go to Inbound security rules and add the four rules in the order described in the Network Security Group Rules section above.

**Step 5 — Associate NSG with Subnets**

Inside Portfolio-NSG go to Subnets and associate Portfolio-NSG with both Web-Subnet and DB-Subnet inside Portfolio-VNet.

**Cleanup**

To avoid any charges after completing the lab, navigate to PortfolioVNet-RG and click Delete resource group. Type the resource group name to confirm. This removes every resource created in this lab.

---

## Skills Demonstrated

Microsoft Azure Portal Navigation and Resource Deployment
Virtual Network Design and Subnet Segmentation
Network Security Group Configuration
Inbound and Outbound Security Rule Management
Cloud Network Security Architecture
Principle of Least Privilege Implementation
Defense in Depth Strategy
Two-Tier Network Architecture Design

---

## Tools and Technologies

Microsoft Azure — Cloud platform
Azure Virtual Network (VNet) — Private cloud networking
Azure Network Security Groups (NSG) — Virtual firewall and traffic control
Azure Resource Groups — Resource lifecycle management
draw.io — Architecture diagram creation

---

## About This Project

This lab was built as a portfolio demonstration to show practical Azure networking skills alongside theoretical knowledge. The goal was to move beyond certification rote learning and prove the ability to configure real cloud infrastructure through the Azure Portal.

The architecture mirrors patterns used in production environments where web-facing and data storage layers must be isolated for compliance, security, and operational reasons.



## License

This project is open source and available under the MIT License. You are free to use, reproduce, and adapt this lab for your own learning and portfolio.

---

## Acknowledgements

Microsoft Azure free account program for making hands-on cloud labs accessible to learners without upfront cost.
