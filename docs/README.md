Artefacts written by /build-product land here.

Empty is correct on a fresh clone — the files appear here when you run the pipeline, one
per phase, each read by the phase after it:

| Phase | File |
|---|---|
| 1 Discovery | `validated-idea.md` |
| 2 Specs + UX | `requirements.md`, `user-stories.md`, `ux-wireframes.md` |
| 3 Architecture | `architecture.md`, `implementation-plan.md` |
| 7 Review | `review-report.md`, `security-audit.md` |
| 9 Feedback | `backlog.md` |

The interface contract from Phase 3 is not here — it ships as a real code file with a test
pinning it, next to the source it describes.
