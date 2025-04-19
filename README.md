# pipe-network-node-guide
Backup, Restore, and Configure Pipe Network Node on VPS
# Pipe Network Node Guide

![Status](https://img.shields.io/badge/status-active-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)

Panduan lengkap untuk melakukan **backup**, **restore**, dan **konfigurasi** node **Pipe Network** di VPS.

---

## 1. Backup Node Pipe Network

### Langkah:
1. Masuk VPS lama  
   `ssh root@IP_VPS_LAMA`
2. Hentikan service  
   `sudo systemctl stop pop.service`
3. Backup file penting  
   `tar -czvf /root/backup_pipe.tar.gz -C /root/pipenetwork node_info.json download_cache`
4. Kirim ke VPS baru  
   `scp /root/backup_pipe.tar.gz root@IP_VPS_BARU:/root/`

## 2. Restore di VPS Baru

1. Masuk VPS baru  
   `ssh root@IP_VPS_BARU`
2. Ekstrak backup  
   `mkdir -p /root/pipenetwork`  
   `tar -xzvf /root/backup_pipe.tar.gz -C /root/pipenetwork/`
3. Cek file  
   `ls -lh /root/pipenetwork/`

## 3. Konfigurasi Node

1. Download Pipe POP  
   `curl -L -o pop "https://dl.pipecdn.app/v0.2.8/pop"`  
   `chmod +x pop`
2. Cek konfigurasi  
   `cat /root/pipenetwork/node_info.json`
3. Buka firewall
   sudo ufw allow 8003/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
sudo ufw reload
   4. Jalankan node  
`./pop`

## 4. Menjalankan Sebagai Service

1. Buat service:
   sudo tee /etc/systemd/system/pop.service << ‘EOF’
[Unit]
Description=Pipe POP Node Service
After=network.target
Wants=network-online.target
[Service]
AmbientCapabilities=CAP_NET_BIND_SERVICE
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
ExecStart=/root/pipenetwork/pop 
–ram=isi_ram_mu 
–max-disk=isi_disk_mu 
–cache-dir=/root/pipenetwork/download_cache 
–pubKey=isi_walletmu
Restart=always
RestartSec=5
LimitNOFILE=65536
LimitNPROC=4096
StandardOutput=journal
StandardError=journal
SyslogIdentifier=pop-node
WorkingDirectory=/root/pipenetwork

[Install]
WantedBy=multi-user.target
EOF
2. Reload & start service  
sudo systemctl daemon-reload
sudo systemctl enable pop.service
sudo systemctl start pop.service
3. Cek status  
`sudo systemctl status pop.service`

## 5. Cek Status & Log

- Status node  
`./pop --status`
- Log realtime  
`journalctl -u pop.service -f`
- Log histori  
`journalctl -u pop.service --since "1 hour ago"`

## 6. Troubleshooting

**Node nggak jalan?**
sudo systemctl restart pop.service
sudo systemctl status pop.service

**Port 8003 error?**
sudo ufw allow 8003/tcp
sudo ufw reload

---

> Panduan ini mencakup semua proses mulai dari backup, restore, hingga konfigurasi dan monitoring node Pipe Network.
