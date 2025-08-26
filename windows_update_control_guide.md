# Windows 11 更新控制完整指南

## 系统信息
- **适用版本**：Windows 11 专业版 24H2
- **版本号**：26100.4351
- **创建日期**：2025年8月26日

## ⚠️ 重要提醒
- 此脚本会将Windows更新暂停到2080年（基本永久禁用）
- 执行前请备份注册表和创建系统还原点
- 必须以管理员身份运行

---

## 📋 执行步骤

### 1. 备份当前设置（强烈推荐）
```cmd
reg export "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate" C:\backup_windowsupdate.reg
```

### 2. 创建系统还原点
1. 在开始菜单搜索"创建还原点"
2. 点击"创建"按钮
3. 输入描述："执行Windows更新脚本前"

### 3. 执行暂停更新脚本
**以管理员身份运行命令提示符，复制粘贴以下完整命令：**

```cmd
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "ActiveHoursEnd" /t REG_DWORD /d 00000011 /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "ActiveHoursStart" /t REG_DWORD /d 00000008 /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "AllowAutoWindowsUpdateDownloadOverMeteredNetwork" /t REG_DWORD /d 00000000 /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "AutoRebootLimitInDays" /t REG_DWORD /d 00005000 /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "ExcludeWUDriversInQualityUpdate" /t REG_DWORD /d 00000000 /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "FlightCommitted" /t REG_DWORD /d 00000000 /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "IsExpedited" /t REG_DWORD /d 00000000 /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "LastToastAction" /t REG_DWORD /d 00000000 /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "UxOption" /t REG_DWORD /d 00000000 /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "FlightSettingsMaxPauseDays" /t REG_DWORD /d 00005000 /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseFeatureUpdatesStartTime" /t REG_SZ /d "2024-03-05T06:20:03Z" /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseQualityUpdatesStartTime" /t REG_SZ /d "2024-03-05T06:20:03Z" /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseUpdatesExpiryTime" /t REG_SZ /d "2080-03-31T06:20:25Z" /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseFeatureUpdatesEndTime" /t REG_SZ /d "2080-03-31T06:20:25Z" /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseQualityUpdatesEndTime" /t REG_SZ /d "2080-03-31T06:20:25Z" /f & REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "AllowMUUpdateService" /t REG_DWORD /d 00000000 /f & start ms-settings:windowsupdate-options
```

---

## 🔄 恢复Windows更新方法

### 方法一：删除暂停设置（推荐）
**以管理员身份运行命令提示符，执行以下命令：**

```cmd
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseFeatureUpdatesStartTime" /f
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseQualityUpdatesStartTime" /f
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseUpdatesExpiryTime" /f
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseFeatureUpdatesEndTime" /f
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseQualityUpdatesEndTime" /f
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "FlightSettingsMaxPauseDays" /f
```

### 方法二：修改暂停时间为过去时间
```cmd
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseFeatureUpdatesEndTime" /t REG_SZ /d "2025-01-01T00:00:00Z" /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseQualityUpdatesEndTime" /t REG_SZ /d "2025-01-01T00:00:00Z" /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseUpdatesExpiryTime" /t REG_SZ /d "2025-01-01T00:00:00Z" /f
```

### 方法三：完全重置Windows更新服务
```cmd
net stop wuauserv
net stop cryptSvc
net stop bits
net stop msiserver
ren C:\Windows\SoftwareDistribution SoftwareDistribution.old
ren C:\Windows\System32\catroot2 catroot2.old
net start wuauserv
net start cryptSvc
net start bits
net start msiserver
```

### 方法四：使用备份文件恢复
如果您之前创建了备份，双击 `C:\backup_windowsupdate.reg` 文件即可恢复原始设置。

---

## 📝 各项设置详细说明

| 设置项 | 值 | 含义 |
|--------|----|----|
| ActiveHoursStart | 8 | 活跃时间开始：上午8点 |
| ActiveHoursEnd | 17 | 活跃时间结束：下午5点 |
| AllowAutoWindowsUpdateDownloadOverMeteredNetwork | 0 | 禁止按流量计费网络自动下载 |
| AutoRebootLimitInDays | 20000 | 自动重启限制：约55年 |
| UxOption | 0 | 禁用更新用户界面选项 |
| PauseUpdatesExpiryTime | 2080-03-31 | 更新暂停到期时间：2080年 |
| AllowMUUpdateService | 0 | 禁用Microsoft Update服务 |

---

## ✅ 验证执行结果

执行脚本后，进行以下验证：

1. **检查Windows设置**
   - 打开 设置 → Windows更新
   - 应显示"更新已暂停"相关信息

2. **验证注册表更改**
   ```cmd
   REG QUERY "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseUpdatesExpiryTime"
   ```

3. **重启计算机**
   - 重启后再次检查Windows更新设置

---

## 🛡️ 安全注意事项

- **定期检查安全性**：虽然更新被暂停，但建议定期关注安全漏洞信息
- **手动下载重要补丁**：对于严重安全漏洞，可考虑手动下载补丁
- **保留恢复方法**：始终保留恢复更新的能力
- **备份重要数据**：定期备份重要文件和数据

---

## 📞 故障排除

**如果更新设置无法生效：**
1. 确认以管理员权限执行
2. 检查Windows更新服务是否正在运行
3. 重启计算机后再次检查
4. 使用系统还原点恢复

**如果需要紧急恢复更新：**
1. 使用方法一删除暂停设置
2. 重启Windows更新服务
3. 在设置中手动检查更新

---

## 📋 快捷批处理文件

### 暂停更新脚本 (pause_updates.bat)
```batch
@echo off
echo 正在暂停Windows更新到2080年...
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseUpdatesExpiryTime" /t REG_SZ /d "2080-03-31T06:20:25Z" /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseFeatureUpdatesEndTime" /t REG_SZ /d "2080-03-31T06:20:25Z" /f
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseQualityUpdatesEndTime" /t REG_SZ /d "2080-03-31T06:20:25Z" /f
echo 完成！请重启计算机。
pause
```

### 恢复更新脚本 (resume_updates.bat)
```batch
@echo off
echo 正在恢复Windows更新...
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseFeatureUpdatesStartTime" /f
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseQualityUpdatesStartTime" /f
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseUpdatesExpiryTime" /f
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseFeatureUpdatesEndTime" /f
REG DELETE "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v "PauseQualityUpdatesEndTime" /f
echo 完成！请重启计算机并检查更新。
pause
```

---

**文档版本**：v1.0  
**最后更新**：2025年8月26日  
**适用系统**：Windows 11 专业版 24H2 (26100.4351)