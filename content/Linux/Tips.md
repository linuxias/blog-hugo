+++
title = "Tips"
+++


## 시스템에서 kernel config 확인하기

본인의 리눅스 시스템 마다 3개 중 하나를 사용할 수 있습니다.

```bash
cat /proc/config.gz
cat /boot/config
cat /boot/config-$(uname -r)
```


