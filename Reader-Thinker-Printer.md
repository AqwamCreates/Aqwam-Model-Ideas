# Reader-Thinker-Printer
> A Parallel Thinker With Arbitary Input And Output Sizes.

INPUT TEXT (Serial Stream)
      │
      ▼
┌────────────────────────────────────────────────────────────────┐
│  PATH A: THE READER (Input RNN)                                │
│  [Token 1] → [Hidden State 1] → ... → [Final Hidden State H_in]│
└───────────────────────────┬────────────────────────────────────┘
                            │
                            ▼
                  ┌─────────────────┐
                  │   LATENT Z_in   │ ← (Compressed Meaning)
                  └────────┬────────┘
                           │
           ╭───────────────┴───────────────╮
           │                               │
           ▼                               ▼
┌───────────────────┐           ┌───────────────────────────────┐
│  PATH B: LOGIC    │           │  PATH C: RESNET SKIP (HIGHWAY)│
│  (Autoencoder)    │           │                               │
│                   │           │  Directly passes H_in (or     │
│  Transforms       │           │  projected version) straight  │
│  Z_in → Z_out     │           │  to the output stage.         │
│  (Pure Reasoning) │           │                               │
│                   │           │  "Don't forget the raw data!" │
└─────────┬─────────┘           └───────────────┬───────────────┘
          │                                     │
          │             ╭───────────────────────╯
          │             │  (Element-wise Add or Concat)
          ▼             ▼
┌───────────────────────────────────────────────────────────────┐
│  COMBINED STATE: [Z_out (Logic) + H_in (Raw Info)]            │
│  This is the input to the Output Stage.                       │
└───────────────────────────┬───────────────────────────────────┘
                            │
                            ▼
┌───────────────────────────────────────────────────────────────┐
│  PATH D: THE PRINTER (Output RNN)                             │
│                                                               │
│  At every time step t:                                        │
│  Input = [Previous Token] + [COMBINED STATE]                  │
│                                                               │
│  The RNN generates tokens using BOTH the new logic plan       │
│  AND the original raw context simultaneously.                 │
└───────────────────────────┬───────────────────────────────────┘
                            │
                            ▼
OUTPUT TEXT (Serial Stream)
