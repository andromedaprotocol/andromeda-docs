---
description: >-
  The Kernel is the very core of the Andromeda Operating System. The entirety of
  the system is enforced by this contract. The permissioning, flow, enforcement,
  etc are all handled at this central point.
---

# aOS Kernel

By and large, the aOS Kernel will be transparent to the user, just as the Linux Kernel sits in the background and does it job without much fuss (if you're lucky). The kernel functions as the frontline for interactions with the system by users.

Before a user interacts with an ADO, the Kernel will do a few checks to ensure that things like the message format is correct, the user has permissions to access or execute the ADO, or that the software/platform fees are enforced.
