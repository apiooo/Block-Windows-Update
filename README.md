# Windows更新控制脚本文件

## 1. 批处理文件 (.bat) - 推荐使用

### 暂停更新脚本 (pause_windows_updates.bat)
```batch
@echo off
title Windows更新暂停脚本
echo ====================================
echo    Windows更新暂停脚本
echo ====================================
echo.
echo 警告：此脚本将暂停Windows更新到2080年
echo 执行前请确保您已备份注册表
echo.
pause
echo.
echo 正在执行注册表修改...
echo.

REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "ActiveHoursEnd" /t REG_DWORD /d 00000011 /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "ActiveHoursStart" /t REG_DWORD /d 00000008 /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "AllowAutoWindowsUpdateDownloadOverMeteredNetwork" /t REG_DWORD /d 00000000 /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "AutoRebootLimitInDays" /t REG_DWORD /d 00005000 /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "ExcludeWUDriversInQualityUpdate" /t REG_DWORD /d 00000000 /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "FlightCommitted" /t REG_DWORD /d 00000000 /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "IsExpedited" /t REG_DWORD /d 00000000 /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "LastToastAction" /t REG_DWORD /d 00000000 /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "UxOption" /t REG_DWORD /d 00000000 /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "FlightSettingsMaxPauseDays" /t REG_DWORD /d 00005000 /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseFeatureUpdatesStartTime" /t REG_SZ /d "2024-03-05T06:20:03Z" /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseQualityUpdatesStartTime" /t REG_SZ /d "2024-03-05T06:20:03Z" /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseUpdatesExpiryTime" /t REG_SZ /d "2080-03-31T06:20:25Z" /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseFeatureUpdatesEndTime" /t REG_SZ /d "2080-03-31T06:20:25Z" /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseQualityUpdatesEndTime" /t REG_SZ /d "2080-03-31T06:20:25Z" /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "AllowMUUpdateService" /t REG_DWORD /d 00000000 /f

echo.
echo 注册表修改完成！
echo 正在打开Windows更新设置...
start ms-settings:windowsupdate-options

echo.
echo ====================================
echo 脚本执行完成！
echo 建议重启计算机以确保设置生效
echo ====================================
pause
```

### 恢复更新脚本 (resume_windows_updates.bat)
```batch
@echo off
title Windows更新恢复脚本
echo ====================================
echo    Windows更新恢复脚本
echo ====================================
echo.
echo 此脚本将恢复Windows更新功能
echo.
pause
echo.
echo 正在删除暂停更新的注册表项...
echo.

REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseFeatureUpdatesStartTime" /f 2>nul
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseQualityUpdatesStartTime" /f 2>nul
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseUpdatesExpiryTime" /f 2>nul
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseFeatureUpdatesEndTime" /f 2>nul
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseQualityUpdatesEndTime" /f 2>nul
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "FlightSettingsMaxPauseDays" /f 2>nul

echo.
echo 恢复完成！
echo 正在打开Windows更新设置...
start ms-settings:windowsupdate-options

echo.
echo ====================================
echo 脚本执行完成！
echo 现在可以正常检查和安装更新了
echo ====================================
pause
```

## 2. PowerShell脚本 (.ps1)

### 暂停更新脚本 (Pause-WindowsUpdates.ps1)
```powershell
# Windows更新暂停脚本
# 需要管理员权限运行

param()

# 检查管理员权限
if (-NOT ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole] "Administrator")) {
    Write-Host "错误: 需要管理员权限运行此脚本" -ForegroundColor Red
    Write-Host "请右键点击PowerShell，选择'以管理员身份运行'" -ForegroundColor Yellow
    Read-Host "按回车键退出"
    exit
}

Write-Host "=====================================" -ForegroundColor Cyan
Write-Host "    Windows更新暂停脚本" -ForegroundColor Cyan
Write-Host "=====================================" -ForegroundColor Cyan
Write-Host ""
Write-Host "警告：此脚本将暂停Windows更新到2080年" -ForegroundColor Yellow
Write-Host "执行前请确保您已备份注册表" -ForegroundColor Yellow
Write-Host ""
Read-Host "按回车键继续，或Ctrl+C取消"

Write-Host ""
Write-Host "正在执行注册表修改..." -ForegroundColor Green

$regPath = "HKLM:\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings"

# 确保注册表路径存在
if (!(Test-Path $regPath)) {
    New-Item -Path $regPath -Force | Out-Null
}

# 应用所有注册表设置
$settings = @{
    "ActiveHoursEnd" = [int]17
    "ActiveHoursStart" = [int]8
    "AllowAutoWindowsUpdateDownloadOverMeteredNetwork" = [int]0
    "AutoRebootLimitInDays" = [int]20000
    "ExcludeWUDriversInQualityUpdate" = [int]0
    "FlightCommitted" = [int]0
    "IsExpedited" = [int]0
    "LastToastAction" = [int]0
    "UxOption" = [int]0
    "FlightSettingsMaxPauseDays" = [int]20000
    "AllowMUUpdateService" = [int]0
}

$stringSettings = @{
    "PauseFeatureUpdatesStartTime" = "2024-03-05T06:20:03Z"
    "PauseQualityUpdatesStartTime" = "2024-03-05T06:20:03Z"
    "PauseUpdatesExpiryTime" = "2080-03-31T06:20:25Z"
    "PauseFeatureUpdatesEndTime" = "2080-03-31T06:20:25Z"
    "PauseQualityUpdatesEndTime" = "2080-03-31T06:20:25Z"
}

# 应用DWORD设置
foreach ($setting in $settings.GetEnumerator()) {
    Set-ItemProperty -Path $regPath -Name $setting.Key -Value $setting.Value -Type DWord
    Write-Host "✓ $($setting.Key) = $($setting.Value)" -ForegroundColor Gray
}

# 应用字符串设置
foreach ($setting in $stringSettings.GetEnumerator()) {
    Set-ItemProperty -Path $regPath -Name $setting.Key -Value $setting.Value -Type String
    Write-Host "✓ $($setting.Key) = $($setting.Value)" -ForegroundColor Gray
}

Write-Host ""
Write-Host "注册表修改完成！" -ForegroundColor Green
Write-Host "正在打开Windows更新设置..." -ForegroundColor Blue
Start-Process "ms-settings:windowsupdate-options"

Write-Host ""
Write-Host "=====================================" -ForegroundColor Cyan
Write-Host "脚本执行完成！" -ForegroundColor Green
Write-Host "建议重启计算机以确保设置生效" -ForegroundColor Yellow
Write-Host "=====================================" -ForegroundColor Cyan
Read-Host "按回车键退出"
```

## 3. 注册表文件 (.reg)

### 暂停更新注册表文件 (pause_updates.reg)
```registry
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings]
"ActiveHoursEnd"=dword:00000011
"ActiveHoursStart"=dword:00000008
"AllowAutoWindowsUpdateDownloadOverMeteredNetwork"=dword:00000000
"AutoRebootLimitInDays"=dword:00005000
"ExcludeWUDriversInQualityUpdate"=dword:00000000
"FlightCommitted"=dword:00000000
"IsExpedited"=dword:00000000
"LastToastAction"=dword:00000000
"UxOption"=dword:00000000
"FlightSettingsMaxPauseDays"=dword:00005000
"PauseFeatureUpdatesStartTime"="2024-03-05T06:20:03Z"
"PauseQualityUpdatesStartTime"="2024-03-05T06:20:03Z"
"PauseUpdatesExpiryTime"="2080-03-31T06:20:25Z"
"PauseFeatureUpdatesEndTime"="2080-03-31T06:20:25Z"
"PauseQualityUpdatesEndTime"="2080-03-31T06:20:25Z"
"AllowMUUpdateService"=dword:00000000
```

### 恢复更新注册表文件 (resume_updates.reg)
```registry
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings]
"PauseFeatureUpdatesStartTime"=-
"PauseQualityUpdatesStartTime"=-
"PauseUpdatesExpiryTime"=-
"PauseFeatureUpdatesEndTime"=-
"PauseQualityUpdatesEndTime"=-
"FlightSettingsMaxPauseDays"=-
```

## 使用说明

### BAT文件使用方法：
1. 复制对应的代码到记事本
2. 保存为 `.bat` 文件（如：`pause_updates.bat`）
3. 右键选择"以管理员身份运行"

### PowerShell文件使用方法：
1. 复制代码到记事本
2. 保存为 `.ps1` 文件
3. 右键PowerShell，选择"以管理员身份运行"
4. 执行：`Set-ExecutionPolicy Bypass -Scope Process -Force`
5. 运行脚本：`.\Pause-WindowsUpdates.ps1`

### REG文件使用方法：
1. 复制代码到记事本
2. 保存为 `.reg` 文件
3. 双击运行（会提示确认）

**推荐使用BAT文件**，因为它提供了最好的用户体验和错误处理。
