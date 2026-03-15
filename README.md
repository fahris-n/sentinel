# Sentinel: A Personal API Gateway

**Sentinel** is a project I built to learn how to protect backend services at scale. It’s a Go-based "bodyguard" that sits in front of an API to handle the two things that usually break a service: traffic spikes and unauthorized access.

Instead of just writing a standalone application, I wanted to understand how infrastructure—specifically **Kubernetes (EKS)** and **AWS networking**—interacts with a real-world system to provide security and resilience.

## 🛠 What it actually does

* **Stops API Abuse:** It uses a "Token Bucket" strategy backed by **Redis** to track requests in real-time. If a user hits their limit, Sentinel intercepts the request and returns a **`429 Too Many Requests`** error before the backend is ever touched.
* **Checks Identity:** Rather than just relying on IP addresses, it validates **JWTs** at the edge. If a token is missing, expired, or tampered with, the gateway returns a **`401 Unauthorized`**, preventing unverified traffic from entering the network.
* **Zero-Trust Isolation:** The entire system lives in a custom **4-tier VPC**. The backend services are kept in a completely private subnet—the only way to reach them is through the Sentinel gateway.

## 🏗 Why I built it this way

I chose **Go** for its concurrency model, which is perfect for high-speed proxying, and **Redis** because it’s the gold standard for sharing state across a cluster of pods. 


## 💻 Tech Stack

* **Language:** Go (Golang)
* **Orchestration:** Kubernetes (Amazon EKS)
* **Data Store:** Redis (for distributed rate-limit state)
* **Cloud:** AWS (VPC, ALB, IAM)
* **Security:** JSON Web Tokens (JWT)

## 📊 Architecture

![Architecture](docs/sentinel-arch.svg)

The architecture follows a strict separation between the **Control Plane** (ALB Controller & K8s API) and the **Data Plane** (User traffic flowing through the ALB to Go Sentinel Pods). This ensures that management tasks never interfere with the latency or availability of the gateway logic.
