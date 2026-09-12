<!-- https://developers.home-assistant.io/docs/apps/presentation#keeping-a-changelog -->

## 1.0.1

- Fix bootloop: the AppArmor profile granted no network access at all
  (`abstractions/base` does not include network rules), so kiwix-serve was
  killed by SIGBUS on its first `socket()` call (getifaddrs at startup) and
  s6 restarted it in an endless loop. Added inet/inet6/netlink rules to both
  the app profile (healthcheck) and the kiwix-serve child profile.
- Fix AppArmor `signal (receive) peer=*_kiwiks` never matching: the Supervisor
  renames the profile to the app slug, so the peer glob excluded the actual
  profile name and the service could not be stopped cleanly.
- Add `m` permission on the kiwix-serve binary in the child profile
  (required for the exec profile transition).

## 1.0.0

- Initial release: kiwix-serve 3.8.2 (official musl binaries), port 8080,
  ZIM library read from HA-mounted NAS storage (/media or /share).
