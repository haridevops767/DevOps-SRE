1. CrashLoopBackOff (Application Crash)

Scenario: A pod keeps restarting due to an application error
Steps to Reproduce

# kubectl run crashpod --image=busybox -- /bin/sh -c "exit 1"

![image](https://github.com/user-attachments/assets/b307bfd9-fce0-4ba5-b6df-7f881d895367)

Troubleshooting:
# kubectl get pods crashpod
# kubectl describe pod crashpod

Solution

Fix the application crash.

# kubectl run crashpod --image=busybox -- /bin/sh -c "sleep 120"

Update the pod configuration to use the correct command.

![image](https://github.com/user-attachments/assets/d6bc52a9-51e4-46ae-bc20-f7e4da48ca58)

