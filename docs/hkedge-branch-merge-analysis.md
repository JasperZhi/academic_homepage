# Cross-Boarder-Data-Cloud-CUHKSZ 分支合并分析报告

> 生成时间：2026-07-08  
> 分析范围：`Sandbox_0702` vs `android_merged` vs `develop`

---

## 1. 分支概览

| 分支 | HEAD Commit | 领先 base 的 commit 数 | 方向 |
|------|------------|----------------------|------|
| `main` | `ce3daf9` | — | 主分支 |
| `Sandbox_0702` | `36998c3` | **20 commits** | iOS + React Native 全量功能开发 |
| `android_merged` | `7d2bba9` | **4 commits** | Android 通知/结算 + 文档 |
| `develop` | `d10be6a` | **1 commit** | "Merge iOS and Android versions" |

**公共 merge base**: `6a9bb6e`（"Rename Copy of phase2.drawio.png to workflow_diagram.png"）

三个分支从同一个 commit 分叉，之后各自独立发展。

---

## 2. Sandbox_0702 vs android_merged 核心区别

### 2.1 技术架构差异

| 维度 | Sandbox_0702 | android_merged |
|------|-------------|----------------|
| **状态管理** | ✅ 移除了 `isHydrated` hydration 等待逻辑，简化启动 | ❌ 保留 `isHydrated`、`createTransition` 等复杂状态机 |
| **API 层** | ✅ 全面重构 `api/index.js`，统一服务层（禁止 screen 直接 fetch） | ❌ 删除了 `bookingApi`、`vaultApi` 导出 |
| **导航路由** | ✅ 保留 Booking / HealthVault / BookingList 三个核心页面 | ❌ 删除了 Booking / HealthVault / BookingList，新增 HospitalClaimFlow / HealthCloudDrive / DoctorAppointment / Notifications |
| **i18n 翻译** | ✅ 1800+ 行增量，全面覆盖 3 种语言 | ❌ 仅 1 行新增 |
| **Server 端** | ✅ 新增 bookings/vault_files 建表 + 扫码验证/预约取号/云盘 API | ✅ 基础 server 代码（与 develop 一致） |

### 2.2 功能模块差异

| 功能 | Sandbox_0702 | android_merged |
|------|:-----------:|:-------------:|
| 🏠 首页（HomeScreen） | ✅ 大幅重写：新增预约挂号卡片、排队叫号通知、取号 modal、二维码 ticket | ⚠️ 小幅改动 |
| 📅 在线预约（BookingScreen/List） | ✅ 保留并更新 | ❌ **完全删除** |
| 🏥 医院理赔流程（HospitalClaimFlow） | ❌ **完全删除** | ✅ 新增完整流程 |
| ☁️ 健康云盘（HealthVaultScreen） | ✅ 保留（小幅调整：SafeAreaView 改用内置） | ❌ **完全删除** |
| 📁 云端硬盘（HealthCloudDrive） | ❌ **完全删除** | ✅ 新增 |
| 👨‍⚕️ 医生预约（DoctorAppointment） | ❌ **完全删除** | ✅ 新增 |
| 🔔 通知中心（Notifications） | ❌ **完全删除** | ✅ 新增 |
| 📋 理赔详情/列表 | ✅ 大幅重写（450-550 行 diff） | ✅ 等量重写 |
| 🎫 注册流程（RegistrationScreen） | ✅ 大幅重写（540 行 diff） | ✅ 等量重写 |
| 👤 个人中心（ProfileScreen） | ✅ 90 行改动 | ⚠️ 无改动 |
| 📱 QR 码页面（QRCodeScreen） | ✅ 200 行改动（重构刷新逻辑、预约取号 QR） | ⚠️ 15 行微调 |
| 👴 长者模式（ElderHomeScreen） | ✅ 110 行改动（语言切换增强） | ⚠️ 15 行微调 |
| 🏷️ 分类/活动页 | ✅ 大幅重写 | ✅ 等量重写 |

### 2.3 独有文件

**Sandbox_0702 独有**（不在 develop 中）：
- `.agents/` — 完整的 AI 开发技能库（dev/test/writing/obsidian 等 ~80 个文件）
- `HKEdgeApp_Appetize.zip` — Appetize 模拟器打包产物
- `HKEdgeApp_Source.zip` — 完整源码打包产物

**android_merged 独有**（不在 develop 中）：
- `.idea/` — Android Studio 项目配置
- `README_HKEdgeAPP_AMYUpdate.md` — AMY 更新说明文档
- `README_merged.md` — 合并说明文档

**develop 独有**（不在 Sandbox_0702 中）：
- `android/app/src/main/java/com/hkedgeapp/ShareModule.kt` — Android 原生分享模块
- `android/app/src/main/java/com/hkedgeapp/SharePackage.kt` — Android 原生分享注册
- `android/app/src/main/res/xml/file_paths.xml` — Android FileProvider 配置
- `api/appointmentApi.js` — 预约 API 模块
- `api/cloudDriveApi.js` — 云盘 API 模块
- `api/hospitalClaimFlowApi.js` — 医院理赔 API 模块
- `api/notificationApi.js` — 通知 API 模块
- `declarations.d.ts` — TypeScript 类型声明
- `jest.setup.js` — 测试初始化
- `MERGE_NOTES.md` — 合并备注
- `screen/DoctorAppointmentScreen.js` — 医生预约页面
- `screen/HealthCloudDriveScreen.js` — 健康云盘页面
- `screen/HospitalClaimFlowScreen.js` — 医院理赔流程页面
- `screen/NotificationsScreen.js` — 通知中心页面

---

## 3. 20 个冲突文件详细分析

两个分支都修改了以下文件（相对于 develop），合并时 **必定冲突**：

### 3.1 🔴 严重冲突（逻辑互斥）

| # | 文件 | Sandbox 行为 | android_merged 行为 | 冲突本质 |
|---|------|-------------|-------------------|---------|
| 1 | **App.tsx** | 保留 Booking/HealthVault/BookingList；删除 HospitalClaimFlow/HealthCloudDrive/DoctorAppointment/Notifications | 删除 Booking/BookingList/HealthVault；保留 HospitalClaimFlow/HealthCloudDrive/DoctorAppointment/Notifications | **路由注册互斥** — 两个分支删除了不同的页面 |
| 2 | **api/index.js** | 完全重写为统一服务层架构（含所有模块） | 删除 bookingApi/vaultApi 的 re-export | **API 架构不同** — Sandbox 重写了整个文件 |
| 3 | **context/UserContext.js** | 移除 `isHydrated`/`createTransition`/`setPreviewPolicies`/`cancelPreview` | 移除 `setPreviewPolicies`/`cancelPreview` | **状态管理逻辑不同** — 都删了东西，但删的不一样 |

### 3.2 🟡 中度冲突（功能改动方向不同）

| # | 文件 | Sandbox 改动量 | android 改动量 | 冲突描述 |
|---|------|:---:|:---:|---|
| 4 | **screen/HomeScreen.js** | 563 行 | 90 行 | Sandbox 新增预约挂号卡片、排队叫号、取号 modal、QR ticket；android 小幅修改 |
| 5 | **screen/QRCodeScreen.tsx** | 199 行 | 15 行 | Sandbox 重构了 QR 刷新逻辑、新增预约取号 QR；android 微调 |
| 6 | **screen/ElderHomeScreen.js** | 110 行 | 15 行 | Sandbox 增强语言切换能力；android 微调 |
| 7 | **screen/ProfileScreen.js** | 90 行 | 0 行 | 仅 Sandbox 改动；android 未触碰到 |
| 8 | **screen/HealthVaultScreen.tsx** | 15 行（保留） | 742 行（**删除整个文件**） | ⚠️ **android 删除了这个文件！** Sandbox 保留并小幅修改 |

### 3.3 🟢 低度冲突（改动量相近，可能内容也相近）

| # | 文件 | Sandbox 改动量 | android 改动量 | 说明 |
|---|------|:---:|:---:|---|
| 9 | **screen/ActivitiesScreen.js** | 146 行 | 146 行 | 改动量完全一致，可能内容相同 |
| 10 | **screen/CategoriesScreen.js** | 138 行 | 138 行 | 改动量完全一致，可能内容相同 |
| 11 | **screen/ClaimDetailsScreen.js** | 461 行 | 458 行 | 改动量非常接近 |
| 12 | **screen/ClaimsListScreen.js** | 547 行 | 534 行 | 改动量非常接近 |
| 13 | **screen/RegistrationScreen.tsx** | 542 行 | 542 行 | 改动量完全一致 |
| 14 | **constants/translations.js** | +1081/-1418 | +1/-117 | Sandbox 大规模重写 i18n；android 几乎不改 |
| 15 | **MERGE_NOTES.md** | 新增 | 删除 | Sandbox 新增文件 vs android 删除（android 没有此文件） |

### 3.4 文件存在性冲突

| # | 文件 | Sandbox | android_merged | develop |
|---|------|:---:|:---:|:---:|
| 16 | **screen/BookingScreen.tsx** | ✅ 保留 | ❌ 删除 | ✅ 存在 |
| 17 | **screen/BookingListScreen.tsx** | ✅ 保留 | ❌ 删除 | ✅ 存在 |
| 18 | **screen/DoctorAppointmentScreen.js** | ❌ 删除 | ✅ 保留 | ✅ 存在 |
| 19 | **screen/HealthCloudDriveScreen.js** | ❌ 删除 | ✅ 保留 | ✅ 存在 |
| 20 | **screen/HospitalClaimFlowScreen.js** | ❌ 删除 | ✅ 保留 | ✅ 存在 |
| 21 | **screen/NotificationsScreen.js** | ❌ 删除 | ✅ 保留 | ✅ 存在 |

---

## 4. 功能直观对比

### Sandbox_0702 侧重 → iOS 完整体验

```
🏠 首页（丰富版）
├── 预约挂号卡片 → BookingScreen → BookingListScreen
├── 排队叫号通知（模拟） → QR 取号 modal
├── 理赔概览
├── 个人健康云盘入口 → HealthVaultScreen
└── 底部 Tab 栏（重新设计）
```

**关键词**: i18n 全覆盖、深浅色主题、长者模式、Appetize 打包、代码注释（中文）

### android_merged 侧重 → 医院业务闭环

```
🏠 首页（精简版）
├── 医院理赔流程 → HospitalClaimFlowScreen
├── 医生预约 → DoctorAppointmentScreen
├── 健康云盘 → HealthCloudDriveScreen
├── 通知中心 → NotificationsScreen
└── 实时结算
```

**关键词**: 通知推送、实时结算、医院 HIT 对接、证据文档、Android 专项

### 合并后理想状态（两者互补）

```
🏠 首页（完整版）
├── 预约挂号卡片 → BookingScreen → BookingListScreen  ← Sandbox
├── 排队叫号通知 → QR 取号 modal                     ← Sandbox
├── 医院理赔流程 → HospitalClaimFlowScreen             ← android
├── 医生预约 → DoctorAppointmentScreen                ← android
├── 健康云盘 → HealthVaultScreen                        ← Sandbox
├── 健康云盘 → HealthCloudDriveScreen                   ← android
├── 通知中心 → NotificationsScreen                      ← android
├── 理赔概览
└── 底部 Tab 栏（重新设计）                            ← Sandbox
```

---

## 5. 合并策略建议

### 推荐方案：分两步合并

```
Step 1: android_merged → develop  （先合并改动小的）
Step 2: Sandbox_0702 → develop    （再合并改动大的）
```

### Step 1: `android_merged` → `develop`

**风险等级**: 🟢 低  
**冲突文件**: 0（develop 领先 android 仅 1 个 commit，且无重叠改动）

```
合并内容:
  ✅ README_merged.md / README_HKEdgeAPP_AMYUpdate.md
  ✅ .idea/ 项目配置
  ✅ server/server.js 基础版
  ✅ 所有 android_merged 的 screen 改动
```

### Step 2: `Sandbox_0702` → `develop`

**风险等级**: 🔴 高  
**预计冲突**: 15-20 个文件

**关键冲突及解决方案**:

| 冲突文件 | 推荐解决策略 |
|---------|------------|
| `App.tsx` | **两边都保留** — 注册所有 7 个 screen（Booking + HealthVault + BookingList + HospitalClaimFlow + HealthCloudDrive + DoctorAppointment + Notifications） |
| `api/index.js` | **采用 Sandbox 版本** — 统一服务层架构更完善，再补上 android 的 API 模块导出 |
| `context/UserContext.js` | **采用 Sandbox 版本** — 简化了 hydration 逻辑，移除冗余状态机 |
| `screen/HealthVaultScreen.tsx` | **保留不删除** — 采用 Sandbox 版本（小幅改动的版本） |
| `screen/HomeScreen.js` | **采用 Sandbox 版本** — 功能更完整（预约卡片+叫号通知），再 port android 的改动 |
| `screen/QRCodeScreen.tsx` | **采用 Sandbox 版本** — 重构更彻底 |
| `screen/ElderHomeScreen.js` | **采用 Sandbox 版本** |
| `screen/ProfileScreen.js` | **采用 Sandbox 版本** |
| `constants/translations.js` | **采用 Sandbox 版本** — 1800+ 行 i18n 完善 |
| `screen/ActivitiesScreen.js` 等 | 逐文件对比，可能两边改动内容一致，选 Sandbox 为主 |
| BookingScreen/List（文件存在性） | **保留**（Sandbox 版本） |
| DoctorAppointment/HealthCloudDrive/HospitalClaimFlow/Notifications | **保留**（android/develop 版本） |
| `MERGE_NOTES.md` | **保留 develop 版本** |
| `.agents/` | **纳入**（Sandbox 独有，AI 开发技能库） |
| Android 原生文件 | **保留**（develop 独有，Sandbox 缺失的 ShareModule 等） |

---

## 6. 风险评估

| 风险项 | 等级 | 说明 |
|--------|:---:|------|
| App.tsx 路由注册合并 | 🔴 高 | 两个分支删了不同的页面，需手工合并 import + Stack.Screen |
| api/index.js 架构冲突 | 🔴 高 | Sandbox 重写了 API 层，android 改了导出 |
| UserContext.js 状态机 | 🟡 中 | Sandbox 删了 isHydrated，需确认不影响 android 功能 |
| HealthVaultScreen 删除 | 🔴 高 | android 删了整文件，需决策保留还是删除 |
| i18n 翻译覆盖 | 🟡 中 | Sandbox 改了 2500+ 行，android 几乎不改，合并量大 |
| 运行时兼容性 | 🟡 中 | 合并后需验证 Booking + HospitalClaimFlow 等功能共存 |

---

## 7. 结论

**可以合并**，两个分支内容互补：
- `Sandbox_0702` 提供：完整的 iOS 端 UI/UX 改进、i18n 多语言、预约挂号、QR 扫码、代码质量改进
- `android_merged` 提供：医院理赔流程、医生预约、通知中心、实时结算、Android 专项文档

建议按 **先 android → 再 sandbox** 的顺序合并，预计需要手动解决 **15-20 个冲突文件**，其中最复杂的是 `App.tsx`（路由注册）和 `api/index.js`（API 架构）。

合并后需要：
1. 检查所有 7 个页面路由是否正常注册
2. 验证 `UserContext` 状态机在各页面间流转是否正确
3. 全量回归测试 i18n 翻译
4. 确认 Android 原生模块（ShareModule 等）未被误删
