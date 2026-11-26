# ZeroTier - Global Area Networking

> This is a fork of ZeroTierOne with the source-available `nonfree/` directory (the network controller) removed, so everything here except `ext/` is MPL-2.0. "ZeroTier" is a trademark of ZeroTier, Inc.; this fork is not affiliated with or endorsed by ZeroTier, Inc.

## Quick Links

* [ZeroTier Documentation](https://docs.zerotier.com) - **Start here for downloads, installation, and usage**
* [How to build](build.md) - **Build instructions and platform requirements**
* [Corporate Site](https://www.zerotier.com/)
* [Downloads](https://www.zerotier.com/download/)
* [Service API Reference](service/README.md)
* [Commercial Support](https://www.zerotier.com/contact)
* [License Information](#license)
* [Changes in this fork](#changes-in-this-fork)

## Changes in this fork

Based on upstream [`zerotier/ZeroTierOne`](https://github.com/zerotier/ZeroTierOne) `dev` at [`899352e`](https://github.com/zerotier/ZeroTierOne/commit/899352e38).

### Upstream pull requests merged

These were still open upstream when they were merged here. Each commit was cherry-picked with `-x`, so it records the original upstream commit.

| PR | Change | Author |
| --- | --- | --- |
| [#2646](https://github.com/zerotier/ZeroTierOne/pull/2646) | Make the PUSH_DIRECT_PATHS rate gate a real window. Relayed peers stopped accepting path hints after about 105 s. | [kirpalricky](https://github.com/kirpalricky) |
| [#2611](https://github.com/zerotier/ZeroTierOne/pull/2611) | Increase physical UDP receive and send buffers (4 MiB / 2 MiB) | [alatteri](https://github.com/alatteri) |
| [#2618](https://github.com/zerotier/ZeroTierOne/pull/2618) | Reclaim closed socket records on `select()` timeout or error. Fixes running out of `Phy`'s 1024 socket records after long offline periods. | [Alek12313](https://github.com/Alek12313) |
| [#2644](https://github.com/zerotier/ZeroTierOne/pull/2644) | Verify network config and credential signatures before taking `Network::_lock`, so config refreshes no longer stall traffic | [kirpalricky](https://github.com/kirpalricky) |
| [#2645](https://github.com/zerotier/ZeroTierOne/pull/2645) | Decode network config and credential packets on a worker thread | [kirpalricky](https://github.com/kirpalricky) |
| [#2642](https://github.com/zerotier/ZeroTierOne/pull/2642) | Recover peer traffic sooner after path failure | [apkd](https://github.com/apkd) |
| [#2641](https://github.com/zerotier/ZeroTierOne/pull/2641) | Retry unanswered root handshakes sooner (after sleep or a network change). The `selftest.cpp` conflict with #2642 was resolved by keeping both tests. | [apkd](https://github.com/apkd) |
| [#2566](https://github.com/zerotier/ZeroTierOne/pull/2566) | Honor `cpuPinningEnabled` in PacketMultiplexer workers. Adapted because it did not apply cleanly; it now reports `pthread_setaffinity_np`'s return code instead of `errno`. | [rebroad](https://github.com/rebroad) |

### Other changes

- **Removed the source-available controller** ([`b86a893`](https://github.com/tiernano/ZeroTierOne/commit/b86a89374), [`7379c60`](https://github.com/tiernano/ZeroTierOne/commit/7379c6003)):
  - deleted `nonfree/`, `objects-nonfree.mk`, the central-controller build script and CI workflow, and `ext/central-controller-docker`;
  - `OneService.cpp` and `EthernetTap.cpp` no longer include controller headers;
  - the `ZT_NONFREE` option and controller CMake presets are gone, and requesting `ZT_NONFREE` or `ZT1_CENTRAL_CONTROLLER` is now a configure error;
  - Makefiles, Debian/RPM packaging and the Windows project no longer reference `nonfree/`.

  As a result this daemon **cannot act as a network controller**. Controller web UIs such as ztnet need an upstream ZeroTier controller.
- **Fixed a dangling frame pointer** ([`c6bf312`](https://github.com/tiernano/ZeroTierOne/commit/c6bf31285)). `Switch::onLocalEthernet` re-injected frames addressed to the node's own MAC on a detached thread that read the caller's buffer after it had been reused. The thread now gets its own copy.
- **Fixed dropped VLAN IDs** ([`c6bf312`](https://github.com/tiernano/ZeroTierOne/commit/c6bf31285)). With multicore enabled, the PacketMultiplexer worker passed VLAN ID `0` instead of the frame's VLAN ID.

## About

ZeroTier is a smart programmable Ethernet switch for planet Earth. It allows all networked devices, VMs, containers, and applications to communicate as if they all reside in the same physical data center or cloud region.

This is accomplished by combining a cryptographically addressed and secure peer-to-peer network (termed VL1) with an Ethernet emulation layer somewhat similar to VXLAN (termed VL2). Our VL2 Ethernet virtualization layer includes advanced enterprise SDN features like fine grained access control rules for network micro-segmentation and security monitoring.

All ZeroTier traffic is encrypted end-to-end using secret keys that only you control. Most traffic flows peer-to-peer, though we offer free (but slow) relaying for users who cannot establish peer-to-peer connections.

Apps for Android and iOS are available for free in the Google Play and Apple app stores.

For repository layout, build instructions, platform requirements, and information about running ZeroTier, see [build.md](build.md).

## License

See [LICENSE-MPL.txt](LICENSE-MPL.txt) for all code in node/, osdep/, service/, and everywhere else except ext/.

The upstream source-available `nonfree/` directory (network controller) is not included in this fork.

Code in ext/ is external code included for build convenience or backward compatibility and retains its original license.
