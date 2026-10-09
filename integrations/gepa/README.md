# GEPA integration

No patch is needed. GEPA saves `gepa_state.bin` in its `run_dir` as it goes, and
`evodetector` reads the per-candidate validation scores from it.

## After a run

```bash
evodetector extract gepa runs/my_gepa_run --tokens-per-call 2249 -o my_run.json
evodetector detect my_run.json
```

`--tokens-per-call` only affects the token columns. See `docs/conventions.md`
for how GEPA candidates map to rounds.

## During a run

When `gepa.optimize(..., run_dir=...)` is given a run directory, recent GEPA
versions automatically stop gracefully once a file named `gepa.stop` appears in
it. Let `evodetector watch` create that file on alarm:

```bash
evodetector watch gepa runs/my_gepa_run --interval 30 --stop-file runs/my_gepa_run/gepa.stop
```

On alarm it prints the selected candidate and writes the monitor state to
`runs/my_gepa_run/evodetector_monitor.json`. For older GEPA versions, pass
`stop_callbacks=[FileStopper("runs/my_gepa_run/gepa.stop")]` yourself.

## Status

The reader reproduces the paper's GEPA cells from the saved states. The live
watch and stop-file path has not yet been exercised against a running GEPA job.
Check the `gepa.stop` behaviour against the GEPA version you pin.
