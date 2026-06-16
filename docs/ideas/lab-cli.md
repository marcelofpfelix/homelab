# Lab CLI Idea

A future `lab` CLI could wrap common homelab workflows.

Potential implementation options:

- Go with Cobra
- Go with Bubble Tea for interactive views
- A small shell wrapper if the command surface stays simple

Candidate commands:

```text
lab inventory
lab lint
lab deploy container <name> --target <host>
lab docs
```
