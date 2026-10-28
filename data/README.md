# Raw test data

- Raw data is never edited. Processing happens in scripts that read from here and write plots.
- CSV or MCAP. Header row, units in column names (`t_s`, `torque_Nm`, `current_A`).
- Name files `YYYY-MM-DD-<test-name>.<ext>` to match the test record in `docs/tests/`.
- Large files (video, big logs, checkpoints) go in Git LFS or a linked external store.
