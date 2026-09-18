powershell

$D = "Ubuntu-24.04"
wsl -d $D -- df -h /
wsl -d $D -u root -- sync
wsl -d $D -u root -- fstrim -v /
wsl --shutdown

$base = (Get-ChildItem "HKCU:\Software\Microsoft\Windows\CurrentVersion\Lxss" |
    Where-Object { $_.GetValue("DistributionName") -eq $D }).GetValue("BasePath")

$vhd = Join-Path $base "ext4.vhdx"
$vhd
(Get-Item $vhd).Length/1024/1024/1024

diskpart
select vdisk file="C:\path\to\ext4.vhdx"
attach vdisk readonly
compact vdisk
detach vdisk
exit

wsl --shutdown
wsl --manage $D --resize 300GB

wsl -d $D -- df -h /
(Get-Item $vhd).Length/1024/1024/1024
