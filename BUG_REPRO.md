# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

服务首次运行可以正常写入样本，但使用同一数据库路径重启后所有业务数据消失，数据库文件本身仍在。请先不要修改代码，定位重启时实际连接到的存储位置并给出文件与查询证据。 调查全程不要修改目标仓库中的生产代码、测试代码或配置。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/coldchain-custody-task-25
- 仓库地址：https://github.com/zhanglei10281852-gif/coldchain-custody-task-25.git
- parent SHA：4c1ac201147637a6ce8aa65ff9a6f573c74e316e

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/coldchain-custody-task-25.git bug-repro
cd bug-repro
git checkout --detach 4c1ac201147637a6ce8aa65ff9a6f573c74e316e
go test ./internal/storage/sqlite -run "^TestRestartRecoversPersistedRows$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/storage/sqlite -run "^TestRestartRecoversPersistedRows$" -count=1
--- FAIL: TestRestartRecoversPersistedRows (0.06s)
    store_test.go:279: recovered sample = {ID: StudyID: OriginSiteID: ExternalRef: SpecimenType: VialCount:0 VolumeMilliLit:0 State: ExpiresAt:0001-01-01 00:00:00 +0000 UTC ShipmentID: QuarantineNote: CreatedAt:0001-01-01 00:00:00 +0000 UTC UpdatedAt:0001-01-01 00:00:00 +0000 UTC Version:0}, error = get sample batch: not found
FAIL
FAIL	github.com/zhanglei10281852-gif/coldchain-custody-base/internal/storage/sqlite	0.070s
FAIL

```

stderr：

```text
warning: internal/storage/sqlite/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/storage/sqlite/store_test.go has type 100755, expected 100644
warning: internal/storage/sqlite/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/storage/sqlite/store_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/storage/sqlite -run "^TestRestartRecoversPersistedRows$" -count=1
--- FAIL: TestRestartRecoversPersistedRows (0.37s)
    store_test.go:279: recovered sample = {ID: StudyID: OriginSiteID: ExternalRef: SpecimenType: VialCount:0 VolumeMilliLit:0 State: ExpiresAt:0001-01-01 00:00:00 +0000 UTC ShipmentID: QuarantineNote: CreatedAt:0001-01-01 00:00:00 +0000 UTC UpdatedAt:0001-01-01 00:00:00 +0000 UTC Version:0}, error = get sample batch: not found
FAIL
FAIL	github.com/zhanglei10281852-gif/coldchain-custody-base/internal/storage/sqlite	0.557s
FAIL

```

stderr：

```text
warning: internal/storage/sqlite/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/storage/sqlite/store_test.go has type 100755, expected 100644
warning: internal/storage/sqlite/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/storage/sqlite/store_test.go has type 100755, expected 100644

```

## 通过条件

准确定位根因，指出具体 Go 文件和符号，解释错误行为如何导致题面症状，并给出实际复现、调用链或持久化证据。 完成时目标仓库代码、测试和配置零改动。
