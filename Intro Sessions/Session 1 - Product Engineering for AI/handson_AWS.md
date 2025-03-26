# Hands-on Guide: Setting Up Qwen 2.1 on AWS EC2 with Ollama

This guide walks you through setting up and running Qwen 2.1, a small but efficient LLM, on a free tier AWS EC2 instance using Ollama.

## 1. Creating an EC2 Instance

1. **Log in to AWS Console**
   - Go to https://aws.amazon.com/console/
   - Create a new free tier account, or sign into your existing account.

2. **Launch an EC2 Instance**
   - Navigate to EC2 dashboard
   - Click "Launch Instance"
   - **Name**: `ollama-qwen-instance` (or your preferred name)
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

## 3. Installing Ollama

1. **Update system packages**
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Install required dependencies**
   ```bash
   sudo apt install -y curl wget
   ```

3. **Install Ollama**
   ```bash
   curl -fsSL https://ollama.com/install.sh | sh
   ```

4. **Verify installation**
   ```bash
   ollama --version
   ```

![Install Ollama](https://raw.githubusercontent.com/kaizengrowth/oklahomai/main/assets/images/step4.png)


## 4. Setting Up and Running Qwen 2.1

1. **Pull the Qwen 2.1 model**
   ```bash
   # For free tier (faster but less capable)
   # ollama pull qwen2:0.5b

   # For 7B parameter model (recommended for t2.large)
   ollama pull qwen2:7b
   ```
   This will download and set up the model (may take several minutes)

2. **Test the model with a simple prompt**
   ```bash
   ollama run qwen2:0.5b "Explain the concept of product engineering for AI in 3 sentences"
   ```

## 5. Running Ollama as a Service

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

## 6. Using the Ollama API

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

## 7. Performance Optimization

1. **Monitor memory usage**
   ```bash
   watch -n 1 free -h
   ```

2. **Create swap space if needed**
   ```bash
   sudo fallocate -l 8G /swapfile
   sudo chmod 600 /swapfile
   sudo mkswap /swapfile
   sudo swapon /swapfile
   echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
   ```

## 8. Running Ollama in the Background

If you want to keep Ollama running after disconnecting:

```bash
nohup ollama serve > ollama.log 2>&1 &
```

## 9. Basic Prompt Examples

Try these prompts to test your Qwen 2.1 model:



## 10. Cleaning Up

When you're done, remember to stop or terminate your EC2 instance to avoid unnecessary charges:

1. In the EC2 dashboard, select your instance
2. Choose "Instance state" and select "Stop" or "Terminate"

**Note**: Stopping the instance will preserve your data but still incur storage costs. Terminating the instance will delete all data.