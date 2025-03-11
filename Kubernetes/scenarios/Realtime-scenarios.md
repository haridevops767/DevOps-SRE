**1. CrashLoopBackOff (Application Crash)**

Scenario: A pod keeps restarting due to an application error.

Steps to Reproduce

# kubectl run crashpod --image=busybox -- /bin/sh -c "exit 1"

![image](https://github.com/user-attachments/assets/b307bfd9-fce0-4ba5-b6df-7f881d895367)

Troubleshooting:
# kubectl get pods crashpod
# kubectl describe pod crashpod

Solution

Fix the application crash.

``` kubectl run crashpod --image=busybox -- /bin/sh -c "sleep 120" ```

Update the pod configuration to use the correct command.

![image](https://github.com/user-attachments/assets/d6bc52a9-51e4-46ae-bc20-f7e4da48ca58)


** 2️⃣ ImagePullBackOff (Invalid Image)

Deploy an invalid image





Steps to Reproduce
![image](https://github.com/user-attachments/assets/df013d1c-38f7-4a2f-91c6-d5e4e9585576)


Troubleshooting:
Kubectl get pods wrong-image

Solution:
Fix the image.


![image](https://github.com/user-attachments/assets/85f3a8ae-a3a6-4a19-9345-6f05eca80656)


** 3. Pending state.

Scenario: Pods are in pending state.

Steps to Reproduce:

Mention High CPU,Memory configuration which is higher then current configuration of node level in resource section.

![image](https://github.com/user-attachments/assets/bd8a0bac-2d46-4c01-ba93-d8056b4960c8)

Troubleshooting:

kubectl get pod high-resource-pod

kubectl describe pod high-resource-pod|tail -3

![image](https://github.com/user-attachments/assets/baf2d788-b637-4edb-9534-fd59a7e815f5)


Solution:

Decrease the resource request values.

![image](https://github.com/user-attachments/assets/27ddb9dc-d81b-4140-9223-017af22df2b4)







