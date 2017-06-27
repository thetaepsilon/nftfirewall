nftfirewall - runtime-configurable firewall using nftables and generator scripts

The idea behind this hacked up set of scripts is that my networking setup uses different network "profiles" for the firewall.
Every time I go to a new wifi network, I tell my system to load an appropriate firewall (and the associated wpa_supplicant configuration).
In other words, when I need to use wifi I do something like "systemctl start wifi@<profile_name>", which loads a firewall and starts wpa_supplicant.

However, this system was only able to select firewalls based on which wifi network I was attached to, as that was what I used predominantly on my work laptop.
When I needed to handle ethernet or USB networks, a situation quickly arose where there was a combinatorial explosion of firewall conf files for each possible combination of wired+wiress networks.

This repository represents my attempt to generalise this network profile selection to work on a per-interface basis.
There is a base nft firewall which includes (as in the include directive) various script-generated fragments.
This setup does use a fair degree of indirection, but this is intentional - the directory in the scripts mentioned as "$confdir" can be kept separate from the "profile" fragments (say by putting it in /run) and modified via commands to set the profile as I roam between networks.

It *is* possible that I could have used rule appending to modify the per-interface firewall chains that way, instead of reloading the entire firewall each time.
However, I opted for the complete reload approach to avoid stale state issues - I find nft's error output, to put it gently, completely useless at times, so I tried to cut my losses with my sanity ;)
(Why do I pain myself with nft? I don't like duplicating rules between iptables and ip6tables.)

Speaking of nft being unhelpful, when using this system and editing static fragments (e.g. a fragment for one of the network profiles), MAKE SURE THERE'S A TRAILING NEWLINE.
nft's include mechanism doesn't seem to automatically add that to the combined text, and it causes all kinds of fun-to-debug issues.
Guide on a miminum setup coming soon.


