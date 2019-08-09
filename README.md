# Using http to upload files from Windows client to Linux server

This is an example, uploading file in c:\temp\file1.jpeg into a Linux server

1. Convert file into Base64:
$outpath = (Join-Path (pwd) 'out_base64.txt'); $inpath = (Join-Path (pwd) 'file1.jpeg'); [IO.File]::WriteAllText($outpath, ([convert]::ToBase64String(([IO.File]::ReadAllBytes($inpath)))))

2. Enable PUT upload in Nginx:
# cat /etc/nginx/sites-enabled/upload 
server {
	listen 9001 default_server;
	client_max_body_size 100M;
	server_name upload.nginx;
	location / {
		root /var/www/upload;
		dav_methods PUT;
	}
}

3. Upload file from Windows powershell:
PS C:\temp> Invoke-RestMethod -Method PUT -Uri "http://10.10.10.10:9001/base64data.txt" -Body $out

4. Convert back to original file on Linux:
# base64 -d base64data.txt > file1.jpeg


Limitation:
Only Powershell upto version 3 support Invoke-RestMethod

Reference:
1. Powershell base64 encoder: https://gist.github.com/t2psyto/f5453707d2658173715f49293d096fe5
2. Ippsec video about enable PUT upload function in Nginx
