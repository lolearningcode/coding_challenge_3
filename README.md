
# Tech Challenge 3: Infrastructure as Code with Terraform and Ansible

This project provisions an AWS EC2 instance and S3 bucket using **Terraform**, then configures the EC2 instance using **Ansible** to install and start an NGINX web server with a custom "Hello, World!" page.

---

## 🛠️ Environment Setup

### ✅ Prerequisites
- AWS CLI configured with valid credentials
- SSH key pair in AWS (used to connect to EC2)
- [Terraform](https://developer.hashicorp.com/terraform/downloads)
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- A Unix-based machine (macOS/Linux) with Bash terminal access

---

## 🚀 Deploy Infrastructure with Terraform

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd tech-challenge-3
```

### 2. Initialize Terraform

```bash
terraform init
```

### 3. Apply the Terraform plan

```bash
terraform apply
```

This will:
- Create a VPC, subnet, and internet gateway
- Launch an EC2 instance
- Create an S3 bucket
- Set up necessary IAM roles and security groups

You’ll get the **EC2 public IP** from the Terraform output.

---

## 🧪 Configure EC2 with Ansible

### 1. Update `inventory.ini`

```ini
[web]
<your-ec2-public-ip> ansible_user=ec2-user ansible_ssh_private_key_file=/Users/yourname/Downloads/your-key.pem ansible_python_interpreter=/usr/bin/python3.8
```

Replace with your actual EC2 IP and path to your `.pem` key.

### 2. Run the Ansible playbook

```bash
ansible-playbook -i inventory.ini web.yml
```

This will:
- Enable `nginx1` via Amazon Linux Extras
- Install NGINX
- Start the service
- Deploy a "Hello, World!" page

---

## 📂 File Structure

```
tech-challenge-3/
├── main.tf                # Terraform infrastructure definitions
├── outputs.tf             # Terraform outputs (e.g. EC2 public IP, bucket name)
├── inventory.ini          # Ansible inventory file
├── web.yml                # Ansible playbook to install NGINX and deploy HTML
├── README.md              # Project documentation
├── .gitignore             # Ignore Terraform state and .pem files
```

---

## 📘 Code Explanation

### Terraform
- **EC2 Instance**: Provisioned with a public IP and security group allowing HTTP (80) and SSH (22).
- **S3 Bucket**: Randomized suffix to avoid naming collisions.
- **IAM**: Role and instance profile for future extensibility.
- **Networking**: Custom VPC, subnet, route table, and internet gateway created manually.

### Ansible
```yaml
---
- name: Configure EC2 with NGINX and Hello World page
  hosts: web
  become: yes

  tasks:
    - name: Enable NGINX in Amazon Linux Extras and install
      shell: |
        amazon-linux-extras enable nginx1
        yum clean metadata
        yum install -y nginx

    - name: Start and enable NGINX service
      service:
        name: nginx
        state: started
        enabled: true

    - name: Replace default HTML with Hello World
      copy:
        content: "<h1>Hello, World!</h1>"
        dest: /usr/share/nginx/html/index.html
```

---

## ✅ Outcome

After running both tools:
- Your EC2 instance is reachable at `http://<public-ip>`
- NGINX is installed and serving your custom page
- Infrastructure and configuration are automated, version-controlled, and reproducible

---

## 🔐 Notes

- Keep your `.pem` file secure — it should **not** be committed to version control.
- If you're using a non-default region, update the provider in `provider.tf`.
