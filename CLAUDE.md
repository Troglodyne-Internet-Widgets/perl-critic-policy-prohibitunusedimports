# Perl::Critic::Policy::ProhibitUnusedImports

This distribution is a Perl::Critic policy. It reports a `use` whose imports nothing else in the file uses.
The policy is in `lib/Perl/Critic/Policy/ProhibitUnusedImports.pm`. The tests are in `t/main.t`.

## Rules

- Do not install the git hooks. The hub installs them before each run.
- Run the tests with `prove -l t`.
- Before you commit, run `perlcritic --profile .perlcriticrc lib t`. Fix the code that it flags. Turn a policy off in `.perlcriticrc` only on purpose, with the reason in a comment beside it.
- The pre-commit hook runs perltidy with `.perltidyrc`.
- The policy loads each module that it checks, so it is unsafe. Tests must pass `-allow-unsafe`.
- Write prose in `README.md`, `Changes` and POD in plain English. Use the `simple-english` skill.
- Never use `--no-verify`.
