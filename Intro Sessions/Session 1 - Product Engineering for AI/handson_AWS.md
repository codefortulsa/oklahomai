# Hands-on Guide: Setting Up Qwen 2.1 on AWS EC2 with Ollama

This guide walks you through setting up and running Qwen 2.1, a small but efficient LLM, on a free tier AWS EC2 instance using Ollama.

## 1. Creating an EC2 Instance

1. **Log in to AWS Console**
   - Go to https://aws.amazon.com/console/
   - Create a new free tier account, or sign into your existing account.

2. **Launch an EC2 Instance**
   - Navigate to EC2 dashboard
   - Click "Launch Instance"
   - **Name**: `ollama-tiny-llm` (or your preferred name)
   - **AMI**: Ubuntu Server 22.04 LTS (free tier eligible)
   - **Architecture**: Choose x86 (64-bit) architecture, not ARM
   
   ![AMI Selection Screen](https://raw.githubusercontent.com/kaizengrowth/oklahomai/main/assets/images/step1.png)
   
  
 3.  - **Instance Type**: t2.micro (minimum recommended, 1 vCPU, 1GB RAM)
     - For better performance: t2.xlarge or t3.xlarge if budget allows
   - **Key Pair**: Create new or select existing SSH key pair

    ![Network Settings](https://raw.githubusercontent.com/kaizengrowth/oklahomai/main/assets/images/step2.png)

  4. - **Network Settings**: Default VPC, Allow SSH traffic from your IP address. Select "Create security group" and click "Edit" to edit security group.
   - **Security Group**: Create new with the following rules:
     - SSH (port 22) from your IP address
     Click "Add Security Group":
     - Custom TCP (port 11434) from your IP (for Ollama API)

![Add custom security group for Ollama](https://raw.githubusercontent.com/kaizengrowth/oklahomai/main/assets/images/step3.png)
    
 5.  - **Configure Storage**: 30GB gp3 (free tier maximum, 30GB+ recommended)
   - Click "Launch Instance"

## 2. Connecting to Your Instance

1. **Find your instance public IP**
   - On EC2 dashboard, select your instance
   - Copy the Public IPv4 address

2. **Connect via SSH**
   - On macOS/Linux terminal:
     ```bash
     chmod 400 your-key-pair.pem
     ssh -i your-key-pair.pem ubuntu@your-public-ip
     ```
   - On Windows, use PuTTY or Windows Terminal

## 3. Create swap space (8GB recommended)

```bash
   sudo fallocate -l 8G /swapfile
   sudo chmod 600 /swapfile
   sudo mkswap /swapfile
   sudo swapon /swapfile
```

## 4. Installing Ollama

**Install Ollama**
   ```bash
   curl -fsSL https://ollama.com/install.sh | sh
   ```

**Verify installation**
   ```bash
   ollama --version
   ```

![Install Ollama](https://raw.githubusercontent.com/kaizengrowth/oklahomai/main/assets/images/step4.png)


## 5. Setting Up and Running a Small LLM

**Pull and run the smallest smollm2 model**
```bash
ollama pull smollm2:135m
ollama run smollm2:135m
```
Test the model with a prompt!

**Pull and run the smallestQwen 2.1 model**
   ```bash
   ollama pull qwen2:0.5b
   ollama run qwen2:0.5b
   ```
Test the model with a prompt!


## 6. Running Ollama as a Service

1. **Start Ollama service**
   ```bash
   sudo systemctl start ollama
   ```

2. **Enable Ollama to start on boot**
   ```bash
   sudo systemctl enable ollama
   ```

3. **Check service status**
   ```bash
   sudo systemctl status ollama
   ```

## 7. Using the Ollama API

Ollama provides a REST API that you can use for more advanced usage:

1. **Generate a response**
   ```bash
   curl -X POST http://localhost:11434/api/generate -d '{
     "model": "qwen2:7b",
     "prompt": "What are three key considerations when building AI products?",
     "stream": false
   }'
   ```

2. **List available models**
   ```bash
   curl http://localhost:11434/api/tags
   ```
   ```

## 8. Running Ollama in the Background

If you want to keep Ollama running after disconnecting:

```bash
nohup ollama serve > ollama.log 2>&1 &
```

## 9.  Cleaning Up

When you're done, remember to stop or terminate your EC2 instance to avoid unnecessary charges:

1. In the EC2 dashboard, select your instance
2. Choose "Instance state" and select "Stop" or "Terminate"

**Note**: Stopping the instance will preserve your data but still incur storage costs. Terminating the instance will delete all data.