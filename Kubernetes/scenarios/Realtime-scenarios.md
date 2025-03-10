1. CrashLoopBackOff (Application Crash)

Scenario: A pod keeps restarting due to an application error
Steps to Reproduce
kubectl run crashpod --image=busybox -- /bin/sh -c "exit 1"

![image](https://github.com/user-attachments/assets/b307bfd9-fce0-4ba5-b6df-7f881d895367)
