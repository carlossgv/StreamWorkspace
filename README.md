# StreamWorkspace

Workspace for the StreamLight Sleep and StreamTweak Sleep forks. The two application repositories are Git submodules; OpenSpec plans, local skills, and Windows build notes live in this repository.

## Clone on Windows

```powershell
git clone --recurse-submodules https://github.com/carlossgv/StreamWorkspace.git
cd StreamWorkspace
```

If you already cloned the workspace without submodules, run `git submodule update --init --recursive` from its root. To pull later changes, run `git pull` followed by `git submodule update --init --recursive`.

## Repositories

- `StreamLight/` → `carlossgv/StreamLight`, branch `main`
- `StreamTweak/` → `carlossgv/StreamTweak`, branch `master`
- `openspec/changes/add-remote-sleep/` → proposal, design, specification, and implementation checklist
- `WINDOWS-BUILD.md` → build and hardware-test steps

The current application commits implement Sleep Client, Host, and Both and prepare side-by-side installers. The OpenSpec checklist still has Windows-only build, automated, and hardware verification tasks. Treat this as development code until those checks pass. Run only one of the original or fork StreamTweak host applications at a time.

Both forks use TCP port 48098 for their authenticated bridge. The original apps use 47998. The forks keep separate settings and pairing; approve the fork client in the fork host after installing.
