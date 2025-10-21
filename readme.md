
# Branch Office Network with Static & Dynamic Routing

## 1. Project Objective

The main goal of this project is to design, configure, and simulate a network connecting a company's **Head Office (HO)** to a new **Branch Office (BO)** using Cisco Packet Tracer.

The project demonstrates and compares two fundamental routing methods to achieve this connectivity:
1.  **Static Routing:** Manually configuring routes on each router.
2.  **Dynamic Routing (RIPv2):** Allowing routers to automatically discover and share routes.

---

## 2. Software Used

* **Cisco Packet Tracer** (Version 8.2.2.0.400)

---

## 3. Network Topology

The network consists of two sites (HO and BO), each with its own local area network (LAN), connected by a point-to-point serial WAN link.

* **Head Office (HO):**
    * 1 x `4331` Router (`HO-Router`)
    * 1 x `2960` Switch (`HO-Switch`)
    * 2 x PCs (`HO-PC`, `HO-PC2`)
* **Branch Office (BO):**
    * 1 x `4331` Router (`BO-Router`)
    * 1 x `2960` Switch (`BO-Switch`)
    * 2 x PCs (`BO-PC`, `BO-PC2`)
* **WAN Connection:**
    * Serial DTE/DCE cable connecting `HO-Router (S0/1/0)` and `BO-Router (S0/1/0)`.



---

## 4. IP Addressing Schema

Three distinct subnets are used:

* **HO LAN:** `192.168.1.0 /24`
* **BO LAN:** `192.168.2.0 /24`
* **WAN Link:** `10.0.0.0 /30`

### Device IP Table

| Device | Location | Interface | IP Address | Subnet Mask | Default Gateway |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **HO-Router** | Head Office | `G0/0/0` | `192.168.1.1` | `255.255.255.0` | `N/A` |
| | | `S0/1/0` (DCE) | `10.0.0.1` | `255.255.255.252` | `N/A` |
| **BO-Router** | Branch Office | `G0/0/0` | `192.168.2.1` | `255.255.255.0` | `N/A` |
| | | `S0/1/0` | `10.0.0.2` | `255.255.255.252` | `N/A` |
| **HO-PC** | Head Office | `Fa0` | `192.168.1.10` | `255.255.255.0` | `192.168.1.1` |
| **HO-PC2** | Head Office | `Fa0` | `192.168.1.11` | `255.255.255.0` | `192.168.1.1` |
| **BO-PC** | Branch Office | `Fa0` | `192.168.2.10` | `255.255.255.0` | `192.168.2.1` |
| **BO-PC2** | Branch Office | `Fa0` | `192.168.2.11` | `255.255.255.0` | `192.168.2.1` |

---

## 5. Configuration

This project can be implemented in two ways, demonstrating both routing methods.

### 5.1. Basic Interface Configuration (Common to Both Scenarios)

The following commands set up the IP addresses on the router interfaces.

**`HO-Router` Config:**
```cisco
enable
configure terminal

! Configure LAN interface
interface GigabitEthernet0/0/0
 description HO LAN Link
 ip address 192.168.1.1 255.255.255.0
 no shutdown
exit

! Configure WAN interface (DCE side)
interface Serial0/1/0
 description WAN Link to BO
 ip address 10.0.0.1 255.255.255.252
 clock rate 64000
 no shutdown
exit

**`BO-Router` Config:**

```cisco
enable
configure terminal

! Configure LAN interface
interface GigabitEthernet0/0/0
 description BO LAN Link
 ip address 192.168.2.1 255.255.255.0
 no shutdown
exit

! Configure WAN interface
interface Serial0/1/0
 description WAN Link to HO
 ip address 10.0.0.2 255.255.255.252
 no shutdown
exit
```

### 5.2. Scenario A: Static Routing Configuration

Static routes are manually added to tell each router how to reach the remote network.

**On `HO-Router` (to reach BO LAN):**

```cisco
configure terminal
! Syntax: ip route [destination_network] [subnet_mask] [next_hop_ip]
ip route 192.168.2.0 255.255.255.0 10.0.0.2
```

**On `BO-Router` (to reach HO LAN):**

```cisco
configure terminal
ip route 192.168.1.0 255.255.255.0 10.0.0.1
```

### 5.3. Scenario B: Dynamic Routing (RIPv2) Configuration

RIP (Routing Information Protocol) is configured to advertise its own connected networks.

*(Note: If converting from Scenario A, you must first remove the static routes using the `no ip route ...` command on each router.)*

**On `HO-Router`:**

```cisco
configure terminal
router rip
 version 2
 network 192.168.1.0
 network 10.0.0.0
 no auto-summary
exit
```

**On `BO-Router`:**

```cisco
configure terminal
router rip
 version 2
 network 192.168.2.0
 network 10.0.0.0
 no auto-summary
exit
```

-----

## 6\. Verification and Testing

Regardless of the routing method used, connectivity can be verified from any PC's Command Prompt.

### 6.1. Ping Tests

From any PC, you should be able to ping all other local and remote PCs.

**Example: From `HO-PC` (192.168.1.10):**

  * **Test local LAN (`HO-PC2`):**
    ```bash
    C:\> ping 192.168.1.11
    Reply from 192.168.1.11: bytes=32 time<1ms TTL=128
    ```
  * **Test remote LAN (`BO-PC`):**
    ```bash
    C:\> ping 192.168.2.10
    Reply from 192.168.2.10: bytes=32 time=30ms TTL=126
    ```
  * **Test remote LAN (`BO-PC2`):**
    ```bash
    C:\> ping 192.168.2.11
    Reply from 192.168.2.11: bytes=32 time=31ms TTL=126
    ```

### 6.2. Check Routing Tables

On either router, you can use the `show ip route` command to see the routing table.

  * In **Scenario A (Static)**, you will see a route marked with `[S]` (Static).
  * In **Scenario B (Dynamic)**, you will see a route marked with `[R]` (RIP).

<!-- end list -->

---

## 7. Supporting Files

This project includes the following additional files:

*   `Branch Office Network.pkt`: The Cisco Packet Tracer project file.
*   `connection addresses.xlsx`: An Excel file with connection addresses.
*   `PC Ping details.xlsx`: An Excel file with PC ping details.
*   Screenshots:
    *   `Screenshot 2025-10-21 132524.png`
    *   `Screenshot 2025-10-21 132558.png`
    *   `Screenshot 2025-10-21 132645.png`
    *   `Screenshot 2025-10-21 132737.png`
    *   `Screenshot 2025-10-21 132841.png`
    *   `Screenshot 2025-10-21 133004.png`
    *   `Screenshot 2025-10-21 133256.png`
    *   `Screenshot 2025-10-21 133353.png`
    *   `Screenshot 2025-10-21 133426.png`
    *   `Screenshot 2025-10-21 133556.png`
```
```