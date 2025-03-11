# AWS VPC Peering Project - README

## Overview
This document provides a step-by-step guide to setting up **VPC Peering** between different VPCs within the **same region** and **different regions**. It includes configuration details, common pitfalls, and troubleshooting tips.

---

## **Scenario 1: VPC Peering in the Same Region**

### **1. Create Two VPCs with Different CIDR Ranges**
To avoid CIDR overlap, create two VPCs in the **same region** with distinct CIDR ranges:
- **Project1-VPC:** `172.0.0.0/16`
- **Project2-VPC:** `10.0.0.0/16`

### **2. Create Subnets, Route Tables, and Internet Gateway**
- Create subnets within each VPC.
- Associate the subnets with respective **Route Tables**.
- Attach an **Internet Gateway (IGW)** to allow internet access for public subnets.


![Screenshot from 2025-03-12 04-19-37](https://github.com/user-attachments/assets/be2cc5ce-6c10-4f3d-bcc7-0cf9a31f1508)

![Screenshot from 2025-03-12 04-19-28](https://github.com/user-attachments/assets/4526d56b-9a3b-4bef-87e2-ae89d8c4a7e6)


### **3. Launch Two EC2 Instances**
- **Instance1** (in `Project1-VPC`)
- **Instance2** (in `Project2-VPC`)

![Screenshot from 2025-03-12 04-20-29](https://github.com/user-attachments/assets/61a762ae-dd39-4148-b242-16531338e381)

### **4. Test Connectivity Before Peering**
- Attempt to ping the **private IP** of `Instance1` from `Instance2` and vice versa.
- The ping will fail because there is no direct connectivity.
  

### **5. Create VPC Peering Connection**
- Navigate to **VPC Dashboard → Peering Connections**
- **Request Peering** between `Project1-VPC` and `Project2-VPC`
- Click **Accept** on the receiving VPC

![Screenshot from 2025-03-12 04-21-01](https://github.com/user-attachments/assets/f17fcfec-d567-4307-9417-240f4f572b26)

![Screenshot from 2025-03-12 04-22-01](https://github.com/user-attachments/assets/6eccc4aa-4fb9-4904-9d96-5c416d1d53d0)



### **6. Update Route Tables for Connectivity**
- Add **Project1-VPC CIDR (172.0.0.0/16)** to `Project2-VPC` Route Table
- Add **Project2-VPC CIDR (10.0.0.0/16)** to `Project1-VPC` Route Table


![Screenshot from 2025-03-12 04-22-40](https://github.com/user-attachments/assets/73c68c03-dbaa-426c-a706-0decd586bc32)

![Screenshot from 2025-03-12 04-22-28](https://github.com/user-attachments/assets/4ac251b3-0b49-4d7b-9a80-c55b81fa51d2)


### **7. Verify Connectivity**
- Ping `Instance1` private IP from `Instance2`
- Ping `Instance2` private IP from `Instance1`
- The connection should now be successful ✅


  ![Screenshot from 2025-03-12 05-07-46](https://github.com/user-attachments/assets/38393b14-3896-44ac-9bb7-0cb5a061e7ea)


  ![Screenshot from 2025-03-12 05-08-22](https://github.com/user-attachments/assets/9cfe6596-3214-4541-8cec-ddd34bbfb812)



### **Issue Faced & Resolution**
Initially, after setting up **VPC Peering in the same region**, I faced an issue where the **ping was still not working**. After investigation, I realized I had **configured the wrong CIDR range** in `Project2-VPC`, causing the issue. Once I rectified the CIDR values, connectivity was successful. **Always double-check the CIDR blocks when setting up peering!** 🚀

---

## **Scenario 2: VPC Peering Across Different Regions**

### **1. Create a New VPC in Another Region**
- Create a **Mumbai Region VPC**: `Diff-Region-VPC (192.0.0.0/16)`

### **2. Follow the Same Steps as Above**
- Create subnets, route tables, and launch EC2 instances in `Diff-Region-VPC`

![Screenshot from 2025-03-12 05-12-56](https://github.com/user-attachments/assets/7180a6f2-dfed-4f3e-a98f-45f17eb1207a)


![Screenshot from 2025-03-12 05-16-04](https://github.com/user-attachments/assets/43223db8-d9ef-4210-8168-1030610144d9)


### **3. Request a Cross-Region Peering Connection**
- Navigate to **VPC Dashboard → Peering Connections**
- Select **Region of Project1-VPC (Ohio)**
- Enter the **VPC ID** of `Project1-VPC` (Ohio)
- Send the request

### **4. Accept Peering Request in Destination Region**
- Go to the **Ohio Region VPC Dashboard**
- Accept the **peering request**
- Now, the connection is established

![Screenshot from 2025-03-12 05-13-58](https://github.com/user-attachments/assets/b0a54959-b869-4f06-8e19-0a89c187e50c)

### **5. Update Route Tables in Both Regions**
- **In Mumbai (Diff-Region-VPC)**: Add `172.0.0.0/16` to the route table
- **In Ohio (Project1-VPC)**: Add `192.0.0.0/16` to the route table

### **6. Verify Connectivity**
- Test **pinging** private IPs between regions
- If routing is configured correctly, the connection should be successful ✅


  ![Screenshot from 2025-03-12 04-37-59](https://github.com/user-attachments/assets/851f52b9-7573-4dc9-bce6-ceaa0dc6d33c)

  ![Screenshot from 2025-03-12 04-38-18](https://github.com/user-attachments/assets/9aaa0640-1772-4726-ac31-5f2b7f271ad8)



---

## **Key Takeaways & Learnings**
1. **VPC Peering does not support transitive routing** – you must set up peering for each VPC that needs connectivity.
2. **Overlapping CIDR blocks are not supported** – use unique CIDR ranges.
3. **Always update route tables** after establishing a peering connection.
4. **Cross-region peering requires selecting the correct region and VPC ID.**
5. **Common Mistake:** Incorrect CIDR configuration can cause connectivity issues. Always double-check CIDR blocks before peering.
6. **Keep learning from mistakes** and improve network troubleshooting skills!

---



