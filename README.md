# ansible-role-create-hidden-service-arch

Deploy tor configs to host hidden services on Archlinux. This role does only the setup of the tor daemon. Please read how you can secure the application behind that here: https://riseup.net/en/security/network-security/tor/onionservices-best-practices

**Advice:** This role lets you deploy multiple hidden services on the same machine. I suggest deploying only one hidden service on each machine. Long story short: If you want to deploy five hidden services you should consider deploy each on a seperate server/container.

## Example config

Here is an example config:

```
hidden_services:
    firstservice:
        key: |
            -----BEGIN RSA PRIVATE KEY-----
            MIICXgIBAAKBgQC0VNwfxIRqCvxO1hoHUgsNF9wdPG9VgM+d5oZCJyx22tfsYo8z
            -----END RSA PRIVATE KEY-----
        access:
            - { external: 1456, internal: '127.0.0.1:6994' }
            - { external: 65433, internal: 'unix:/path/to/socket'}
```

The var `hidden_services` is a dict containing every hidden service you want to deploy. Each key is a new service.
Each hidden service key has:
* A var `key` containing the private key (identity)
* A var `access` telling tor what port to route. This is also a dict containing
  * A var `external`: The out facing port
  * A var `internal`: Where to route traffic from the external port. This can be a TCP server or a UNIX socket.

**Note**: The experienced tor user maybe misses the hostname/onion address variable. Well, it doesn't exist. I expect from you to generate your own key. If you want a random onion address you can find an easy way under `Generate a private key`. If you want a personalized onion address you have to generate it with an external tool like Scallion.

## Generate a private key

You can create the private key outside of tor with:
```
openssl genrsa 1024
```

You should get something like this:
```
-----BEGIN RSA PRIVATE KEY-----
MIICXAIBAAKBgQDOvtreRl/wW4isRDnS+qaf/zC/hsGAnIvt19Z73aNZeQuHHl7c
P5dtePSojGAw28fzSQZvOcitIqOFdh9loWme+7aAdHKUGvmDiso2q0jFrdBgOIxN
iomhpX1KAGbsBEFM7dWHco8t9o1WJIf5BbcufDEVqs8GmYYqnsN2UBOC/QIDAQAB
AoGAasc300UVl9YuqIo0Mzsxg8ERNFos5nfz7Utkg2TdUQCra4XMCITveKLX+ZVG
uPVKN+SnZhq6AnG5ctynKp5LJdumnf6pZuoX6q0rM5j44G5rX9tDYU0GygkUREBz
wfp/xyHvQhuuBdm9stqI6y6W18mUi+1SwSoqquLnPHP3yQECQQDtt7IKVwUV/xpU
hVcd2FGVt7TKCqE3gElVtMLGXyMdFeeeTsohVt5G7FpURYIL4Fwmvg5luSijFV2e
lPANrDXlAkEA3qVdxfXCyTUQxknzL3Cf2FrgQ3ZicZdb8Wg5I3YwgInVO8DLZJSx
KaRl0WF2AvXQF4TlflV4VhJApUk2XhVHOQJAa+NjOq4j8MfG+3VJBJKY9Ipb0xbf
9ICUyAEyJTYoiZrXdFHuCxOR7j53AKhLTXiWWj8H6cMPXnTj+IVALfRqwQJBAKGe
/WF0s7+Hy/OFPwlTAlX2MhIG4BOBQncNp1y8sRy98ZJ8s6kB31FRS7AgxaAEfNh1
2npI5R/mBBN4AAVwANECQDsuc4y5CPQCsje6ot2SlpXuCwHc7AQ0ij54KJsAc18G
mKiLmAK6h/k0JocqpAeLxIp+No+i0dss4prgSM0scfM=
-----END RSA PRIVATE KEY-----
```

## Protip on low entropy servers

If you are in an environment with low entropy you TLS handshakes take longer. You can boost your entropy by setting `hidden_services_boost` to `true`. Default is `false`.  
This will install [haveged](https://wiki.archlinux.org/index.php/Haveged) on the system.
