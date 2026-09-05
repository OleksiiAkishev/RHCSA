Yes — and the key thing is that OnCalendar=, RandomizedDelaySec=, Persistent=, and WantedBy= do not all belong to the same man page.

Your example contains directives from different systemd unit types:

[Unit]
Description=...
Documentation=...

[Timer]
OnCalendar=daily
RandomizedDelaySec=1h
Persistent=true

[Install]
WantedBy=timers.target


A good way to understand systemd configuration is to think:

Section → unit type → man page

1. Which man page describes each thing?
Section	Directive	Look in
[Unit]	Description=	systemd.unit(5)
[Unit]	Documentation=	systemd.unit(5)
[Timer]	OnCalendar=	systemd.timer(5)
[Timer]	RandomizedDelaySec=	systemd.timer(5)
[Timer]	Persistent=	systemd.timer(5)
[Install]	WantedBy=	systemd.unit(5)

So for your particular file:

man systemd.timer


is the primary manual.

And:

man systemd.unit


covers the generic [Unit] and [Install] portions.

2. Why man systemctl logrotate.timer doesn't work

This is an important distinction.

systemctl is the program used to manage units.

logrotate.timer is a unit instance/file, not a manual page.

So:

man systemctl


means:

"Show me the documentation for the systemctl command."

Whereas:

man systemd.timer


means:

"Show me the documentation for the systemd timer unit type."

The .timer suffix tells you the unit is a timer unit.

Similarly:

foo.service
foo.timer
foo.socket
foo.mount
foo.path
foo.target


correspond to different systemd unit types.

3. The most useful systemd man-page map

I would memorize roughly this:

systemd.unit(5)       → common things shared by units
systemd.service(5)    → [Service]
systemd.timer(5)      → [Timer]
systemd.socket(5)     → [Socket]
systemd.path(5)       → [Path]
systemd.mount(5)      → [Mount]
systemd.target(5)     → [Target]
systemd.slice(5)      → [Slice]


So if you encounter:

[Service]
ExecStart=/some/command
Restart=on-failure
User=foo


you immediately go:

man systemd.service


If you encounter:

[Timer]
OnBootSec=5min
OnUnitActiveSec=1h
RandomizedDelaySec=10min


you go:

man systemd.timer


And if you see:

[Install]
WantedBy=multi-user.target


you go:

man systemd.unit

4. How to efficiently search the man pages

This is where Linux's man tools become really powerful.

Suppose you see:

RandomizedDelaySec=1h


and don't know what it means.

First approach: search directly
man systemd.timer


Then inside man:

/RandomizedDelaySec


Press Enter.

Then:

n


for the next match.

This is usually the fastest method.

5. Search all installed man pages

If you don't even know which man page contains the directive, use:

man -K RandomizedDelaySec


This is extremely useful.

-K searches the contents of all installed man pages.

For example:

man -K RandomizedDelaySec


might lead you to:

systemd.timer(5)


Likewise:

man -K OnCalendar


or:

man -K Persistent


This is probably the command you're looking for.

Difference between -k and -K

This distinction is worth remembering:

man -k keyword


searches man-page names and short descriptions.

Whereas:

man -K keyword


searches the actual contents of the man pages.

For configuration directives, -K is often much more useful.

6. apropos is also useful

This:

apropos timer


is essentially the same kind of search as:

man -k timer


You might get things like:

systemd.timer (5) - Timer unit configuration
systemd-system.conf (5) - System and service manager configuration


Then:

man systemd.timer

7. Search for the section itself

A very good systemd habit is:

Don't start by searching for the parameter. Start by identifying the unit type from the section.

For:

[Timer]


run:

man systemd.timer


For:

[Service]


run:

man systemd.service


For:

[Unit]


run:

man systemd.unit


For:

[Install]


also:

man systemd.unit


Then search within it:

/OnCalendar

8. An even better trick: systemd.directives

On many modern RHEL/systemd installations, there is a very useful man page:

man systemd.directives


This is essentially an index of systemd configuration directives.

You can search it:

man systemd.directives


then:

/RandomizedDelaySec


or:

/OnCalendar


It can tell you which systemd manual documents a particular directive.

You can also do:

man -K OnCalendar


but systemd.directives is particularly nice once you're working extensively with systemd.

9. Use man section numbers

You'll frequently see:

systemd.timer(5)
systemctl(1)
systemd.unit(5)


The number is the manual section.

The important ones for this subject are:

1  → commands/programs
5  → configuration files / file formats
7  → miscellaneous
8  → system administration commands


So:

systemctl(1)


is the command documentation.

But:

systemd.timer(5)


is configuration documentation.

That's why you want systemd.timer, not systemctl, to understand:

[Timer]
OnCalendar=daily

10. Don't forget man 5

You can explicitly specify the section:

man 5 systemd.timer


and:

man 5 systemd.unit


This is useful when names collide.

For example:

man 1 systemctl


means the systemctl command.

11. systemctl cat is extremely useful too

There's another important distinction:

Man pages tell you what a directive means.

But when you're trying to understand a real RHEL system, you also want to know:

"Where did this particular unit configuration come from?"

For that:

systemctl cat logrotate.timer


This shows the actual unit file(s) that systemd is using.

Then:

systemctl status logrotate.timer


shows its current state.

And:

systemctl show logrotate.timer


shows the properties systemd has loaded for the unit.

For example:

systemctl show logrotate.timer


can be useful when you're wondering what systemd actually understood from the configuration.

12. Finding where the unit comes from

Another excellent command:

systemctl cat logrotate.timer


Suppose it tells you:

# /usr/lib/systemd/system/logrotate.timer


Now you know the vendor-provided unit is there.

If you have:

/etc/systemd/system/logrotate.timer


that's generally an administrator-created/overridden unit.

You can also ask:

systemctl status logrotate.timer


and:

systemctl show -p FragmentPath logrotate.timer


The latter gives you the unit's main file.

13. Understanding [Install] is slightly different

This one catches many people.

You have:

[Install]
WantedBy=timers.target


It is tempting to think:

"WantedBy= tells systemd how the timer works."

Not quite.

[Install] primarily describes how the unit should be enabled.

When you run:

systemctl enable logrotate.timer


systemd uses the [Install] information to create the appropriate symlink, conceptually something like:

/etc/systemd/system/timers.target.wants/logrotate.timer


So:

[Install]
WantedBy=timers.target


is about installation/enabling, not the timer's scheduling behavior.

The scheduling itself is:

[Timer]
OnCalendar=daily
RandomizedDelaySec=1h
Persistent=true


That's why systemd.timer(5) is the right place for those.

14. A practical workflow I'd recommend

When you encounter an unfamiliar systemd unit, use this workflow.

Step 1 — Identify the sections

For:

[Unit]
...

[Timer]
...

[Install]
...


make a mental map:

[Unit]    → systemd.unit
[Timer]   → systemd.timer
[Install] → systemd.unit

Step 2 — Open the relevant manual
man systemd.timer

Step 3 — Search for the directive

Inside man:

/OnCalendar


then:

n

Step 4 — If you don't know which manual
man -K OnCalendar

Step 5 — Find examples

Search the filesystem:

grep -R "OnCalendar=" /usr/lib/systemd /etc/systemd 2>/dev/null


This is particularly useful for learning from existing units.

Step 6 — Inspect the actual unit
systemctl cat logrotate.timer

Step 7 — Inspect the loaded configuration/state
systemctl show logrotate.timer

Step 8 — Check what the timer is actually doing
systemctl list-timers


or:

systemctl status logrotate.timer

15. One particularly useful command: systemd-analyze

For systemd work, also get familiar with:

systemd-analyze


For example:

systemd-analyze verify /etc/systemd/system/my.timer


can catch configuration problems.

And:

systemd-analyze calendar daily


is useful for understanding calendar expressions.

For example:

systemd-analyze calendar daily


helps you understand what daily actually resolves to.

My "systemd man-page cheat sheet"

When working with systemd, I'd keep this mental model:

                     ┌─────────────────────┐
                     │  What am I looking at? │
                     └──────────┬──────────┘
                                │
              ┌─────────────────┼─────────────────┐
              │                 │                 │
          systemctl          .service          .timer
          command               │                 │
              │                 │                 │
       man systemctl   man systemd.service  man systemd.timer
                                │                 │
                                └───────┬─────────┘
                                        │
                                common directives
                                        │
                                man systemd.unit


And the three commands worth remembering are:

man systemd.timer       # I know it's a timer
man -K OnCalendar       # I don't know where it's documented
man systemd.directives  # I want the systemd directive index


Then, for the actual machine:

systemctl cat foo.timer
systemctl status foo.timer
systemctl show foo.timer
systemctl list-timers


That combination — man pages for semantics + systemctl for the live system + grep for examples — is probably the most effective way to learn systemd on RHEL.

Exactly. The difficult part isn't learning systemd.unit(5) — it's learning how to infer where a piece of configuration is documented.

And unfortunately, the answer is: there isn't a perfectly obvious naming rule. Systemd has a few historical/design conventions that you gradually learn.

The key mental model

Don't think:

[Unit] → systemd.unit
[Install] → systemd.install

Instead think:

"Is this section describing the unit itself, or is it a special lifecycle/configuration mechanism?"

[Unit] and [Install] are both documented in systemd.unit(5) because systemd.unit is effectively the generic/base unit specification.

A .timer is a specific kind of unit, so:

systemd.timer
    ↓
specialized timer behavior

systemd.unit
    ↓
generic behavior shared by units


And [Install] is generic metadata about how a unit gets enabled, rather than a distinct "install unit type."

How would you discover this yourself?

This is where I'd change your approach slightly.

When you see:

[Install]
WantedBy=timers.target


don't immediately guess:

man systemd.install


Instead, ask:

"What kind of object is [Install] describing?"

You can search the installed documentation:

man -K '\[Install\]'


or even:

man -K 'WantedBy'


That should lead you toward systemd.unit(5).

The important thing is that man -K is your escape hatch when your mental model doesn't know the answer yet.

There is an even better trick

On a RHEL system, try:

man systemd.directives


Then search:

/WantedBy


You'll find an index entry along the lines of:

WantedBy=    systemd.unit(5)


This is actually the closest thing to an answer to your question:

"How do I know which man page documents this directive?"

You don't have to know beforehand.

You can use the systemd directive index as a dictionary.

So your workflow can become:

I see an unfamiliar thing
        ↓
Is it obviously a timer/service/socket/etc. directive?
        ↓
       yes ──────→ man systemd.timer
        │
        no
        ↓
man systemd.directives
        ↓
search for the directive
        ↓
find the authoritative man page

But what about [Install] itself?

There's another subtle lesson here.

The section names aren't necessarily mapped one-to-one to man pages.

For example:

[Unit]
Description=
After=
Requires=
Wants=


are generic unit properties.

Then:

[Install]
WantedBy=
RequiredBy=
Alias=
Also=


are also generic unit-related properties.

So both live in:

man systemd.unit


while:

[Timer]
OnCalendar=
OnBootSec=
Persistent=
RandomizedDelaySec=


are timer-specific and therefore:

man systemd.timer


The distinction is generic vs. unit-type-specific, rather than simply "one section = one man page."

A very useful question to ask yourself

When you encounter a systemd directive, ask:

"Would this make sense for a .service, .timer, .socket, and .path as well?"

If yes, there's a good chance it's in:

man systemd.unit


For example:

Description=
Documentation=
After=
Before=
Requires=
Wants=
WantedBy=
Alias=


These aren't fundamentally timer concepts.

Whereas:

OnCalendar=
OnBootSec=
OnUnitActiveSec=
Persistent=
RandomizedDelaySec=


are specifically timer concepts.

So:

generic → systemd.unit
timer-specific → systemd.timer
service-specific → systemd.service
socket-specific → systemd.socket


That's a much more reliable mental model.

And there are some genuine "gotchas"

Systemd documentation has several things that don't follow the naive naming intuition.

For example, you might see:

[Unit]
ConditionPathExists=


and wonder whether there's:

man systemd.condition


No. It's in systemd.unit.

You might see:

[Install]
WantedBy=


and wonder about:

man systemd.install


No. Again, systemd.unit.

You might see:

[Service]
User=
Group=


and think perhaps they're in systemd.user.

No — they're part of the service configuration:

man systemd.service


So trying man systemd.<section> is a reasonable first guess, but don't treat it as a rule.

My preferred "don't get lost" strategy

When learning Linux/systemd, I would use three levels of lookup.

Level 1 — You know the unit type

You see:

[Timer]


Start with:

man systemd.timer


Then search:

/OnCalendar

Level 2 — You know the directive but not the documentation

You see:

WantedBy=timers.target


but don't know where it lives.

Try:

man -K WantedBy


or:

man systemd.directives


This is the important fallback.

Level 3 — You don't understand the whole mechanism

Then don't just search for individual directives.

Read:

man systemd.unit
man systemd.timer


and perhaps:

man systemd.target


because now you're trying to understand how the pieces interact, rather than merely what WantedBy= means.

There's a broader Linux lesson here

What you're encountering is actually a very common Unix/Linux documentation pattern:

The thing you see in a configuration file isn't necessarily the name of the manual page that documents it.

For example, with systemd:

foo.timer
   ↓
systemd.timer(5)

[Install]
   ↓
systemd.unit(5)

WantedBy=
   ↓
systemd.unit(5)

timers.target
   ↓
systemd.special(7) / systemd.target(5), depending on what you're investigating


So don't try to derive the man-page name purely from the syntax.

Instead, develop the habit:

man -K <thing-I-don't-understand>


That's a discovery mechanism, not just a search command.

And once you've found the relevant manual, use its SEE ALSO section heavily. Systemd's man pages are deliberately interconnected.

The workflow I'd personally use

If I were sitting at a RHEL terminal and encountered your file for the first time:

[Unit]
Description=Daily rotation of log files

[Timer]
OnCalendar=daily
RandomizedDelaySec=1h
Persistent=true

[Install]
WantedBy=timers.target


I'd do:

# 1. What is a timer?
man systemd.timer

# 2. Search its directives
/OnCalendar
/RandomizedDelaySec
/Persistent

# 3. What does [Unit] mean?
man systemd.unit

# 4. Where is WantedBy documented?
man -K WantedBy

# 5. See the systemd directive index
man systemd.directives

# 6. See what the actual machine has
systemctl cat logrotate.timer
systemctl status logrotate.timer


That way, you don't need to know beforehand that [Install] belongs to systemd.unit. The documentation itself becomes your map.

That's probably the most important skill here: learn the commands that help you discover the documentation, rather than trying to memorize the documentation hierarchy.