# Cryptography Hands-On Lab

This lab covers various aspects of cryptography including SSH keys, GPG, OpenSSL, and certificate management.

## Table of Contents
1. [SSH Keys and Password-less Authentication](#ssh-keys-and-password-less-authentication)
2. [SSH Hardening](#ssh-hardening)
3. [GPG Key Management](#gpg-key-management)
4. [OpenSSL Operations](#openssl-operations)
5. [Certificate Management](#certificate-management)

## SSH Keys and Password-less Authentication

### 1. Generate SSH Key Pair
```bash
# Generate a new SSH key pair
ssh-keygen -t ed25519 -C "your_email@example.com"

# For legacy systems that don't support Ed25519
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

### 2. Copy Public Key to Remote Server
```bash
# Method 1: Using ssh-copy-id
ssh-copy-id user@remote_host

# Method 2: Manual copy
cat ~/.ssh/id_ed25519.pub | ssh user@remote_host "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

### 3. Test Password-less Login
```bash
ssh user@remote_host
```

## SSH Hardening

### 1. Edit SSH Server Configuration
```bash
sudo nano /etc/ssh/sshd_config
```

Add or modify these security settings:
```
# Disable root login
PermitRootLogin no

# Use SSH protocol 2
Protocol 2

# Disable password authentication
PasswordAuthentication no

# Limit login attempts
MaxAuthTries 3

# Set login grace time
LoginGraceTime 30

# Specify allowed users
AllowUsers user1 user2

# Specify allowed IPs (optional)
AllowUsers user1@192.168.1.*
```

### 2. Restart SSH Service
```bash
sudo systemctl restart sshd
```

## GPG Key Management

### 1. Generate GPG Key Pair
```bash
gpg --full-generate-key
```

### 2. List GPG Keys
```bash
# List public keys
gpg --list-keys

# List private keys
gpg --list-secret-keys
```

### 3. Export GPG Keys
```bash
# Export public key
gpg --armor --export your_email@example.com > public.key

# Export private key
gpg --armor --export-secret-key your_email@example.com > private.key
```

### 4. Sign and Verify Messages
```bash
# Sign a message
echo "Hello, this is a signed message" | gpg --clearsign > signed_message.txt

# Verify a signed message
gpg --verify signed_message.txt
```

## OpenSSL Operations

### 1. Generate Private Key
```bash
# Generate RSA private key
openssl genrsa -out private.key 2048

# Generate EC private key
openssl ecparam -name prime256v1 -genkey -noout -out ec_private.key
```

### 2. Encrypt and Decrypt Files
```bash
# Encrypt a file
openssl enc -aes-256-cbc -salt -in file.txt -out file.enc

# Decrypt a file
openssl enc -aes-256-cbc -d -in file.enc -out file.txt
```

### 3. Create and Sign Digital Signatures
```bash
# Create a signature
openssl dgst -sha256 -sign private.key -out signature.sig file.txt

# Verify a signature
openssl dgst -sha256 -verify public.key -signature signature.sig file.txt
```

## Hash Functions and Encoding

### 1. MD5 Hash
```bash
# Calculate MD5 hash of a file
md5 file.txt

# Calculate MD5 hash of a string
echo -n "Hello World" | md5

# Calculate MD5 hash of multiple files
md5 file1.txt file2.txt file3.txt
```

### 2. SHA Hash Functions
```bash
# Calculate SHA-1 hash
shasum -a 1 file.txt

# Calculate SHA-256 hash
shasum -a 256 file.txt

# Calculate SHA-512 hash
shasum -a 512 file.txt

# Calculate hash of a string
echo -n "Hello World" | shasum -a 256

# Verify file integrity (compare with known hash)
echo "expected_hash  file.txt" | shasum -c
```

### 3. Base64 Encoding/Decoding
```bash
# Encode a file to base64
base64 file.txt > encoded.txt

# Decode a base64 file
base64 -d encoded.txt > decoded.txt

# Encode a string
echo -n "Hello World" | base64

# Decode a string
echo "SGVsbG8gV29ybGQ=" | base64 -d

# Encode/Decode with OpenSSL
openssl base64 -in file.txt -out encoded.txt
openssl base64 -d -in encoded.txt -out decoded.txt
```

### 4. Hash File Integrity Verification
```bash
# Create a checksum file
shasum -a 256 *.txt > checksums.txt

# Verify all files against checksums
shasum -c checksums.txt

# Create MD5 checksums
md5 *.txt > md5checksums.txt

# Verify MD5 checksums
md5 -c md5checksums.txt
```

## Certificate Management

### 1. Generate CSR (Certificate Signing Request)
```bash
openssl req -new -newkey rsa:2048 -nodes -keyout private.key -out certificate.csr
```

### 2. Generate Self-Signed Certificate
```bash
openssl req -x509 -newkey rsa:2048 -nodes -keyout private.key -out certificate.crt -days 365
```

### 3. View Certificate Details
```bash
# View CSR details
openssl req -text -noout -verify -in certificate.csr

# View certificate details
openssl x509 -in certificate.crt -text -noout
```

### 4. Convert Certificate Formats
```bash
# Convert PEM to DER
openssl x509 -in certificate.pem -outform DER -out certificate.der

# Convert DER to PEM
openssl x509 -in certificate.der -inform DER -outform PEM -out certificate.pem
```

## Best Practices and Security Tips

1. Always keep private keys secure and never share them
2. Use strong passwords for key encryption
3. Regularly rotate keys and certificates
4. Use appropriate key sizes (minimum 2048 bits for RSA, 256 bits for EC)
5. Keep software and libraries updated
6. Monitor for suspicious activities
7. Implement proper backup procedures for keys and certificates

## Troubleshooting

### Common SSH Issues
1. Permission problems:
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

2. SSH connection refused:
```bash
# Check SSH service status
sudo systemctl status sshd

# Check firewall rules
sudo ufw status
```

### GPG Issues
1. Key not found:
```bash
# Refresh key database
gpg --refresh-keys
```

2. Bad signature:
```bash
# Verify key validity
gpg --list-sigs
```

## Additional Resources

- [OpenSSL Documentation](https://www.openssl.org/docs/)
- [GPG Manual](https://www.gnupg.org/documentation/manuals/gnupg/)
- [SSH Documentation](https://www.openssh.com/manual.html) 