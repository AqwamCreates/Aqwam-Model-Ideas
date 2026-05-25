# Reader-Thinker-Printer
> A Parallel Thinker With Arbitary Input And Output Sizes.

Architecture:

| STAGE    | COMPONENT            | FUNCTION                                 | DATA FLOW VISUALIZATION                               |
|----------|----------------------|------------------------------------------|-------------------------------------------------------|
| 1. IN    | Input RNN            | Reads serial text; compresses history    | Text --> [RNN] --> H_in (Hidden State)                |
|          |                      | into a single hidden state vector.       |                                                       |
|----------|----------------------|------------------------------------------|-------------------------------------------------------|
| 2. SPLIT | ResNet Link (Pre)    | THE FORK: Splits H_in into two paths:    | H_in splits here:                                     |
|          |                      | 1. Logic Path (to AE)                    |   |---> Path A: Goes to Autoencoder                   |
|          |                      | 2. Highway Path (Skip Connection)        |   |---> Path B: Skips directly to Merger              |
|----------|----------------------|------------------------------------------|-------------------------------------------------------|
| 3. THINK | Autoencoder          | THE BRAIN: Transforms logic in parallel. | [Autoencoder]                                         |
|          |                      | Converts Problem Vector -> Solution      | Takes Path A, outputs Z_out (Solution Plan).          |
|          |                      | Vector. No time steps.                   |                                                       |
|----------|----------------------|------------------------------------------|-------------------------------------------------------|
| 4. MERGE | ResNet Link (Post)   | THE FUSION: Combines New Logic | Raw     | Z_out (from AE)                                       |
|          |                      | Context (from Skip).                     |       |                                               |
|          |                      |                                          | Skip Data (from Path B)                               |
|          |                      |                                          |       =                                               |
|          |                      |                                          | Combined_State                                        |
|----------|----------------------|------------------------------------------|-------------------------------------------------------|
| 5. OUT   | Output RNN           | THE PRINTER: Generates text serially.    | Combined_State --> [RNN] --> Text                     |
|          |                      | Receives Combined_State as CONSTANT      | (RNN sees full context at every single step)          |
|          |                      | context at EVERY time step.              |                                                       |
|----------|----------------------|------------------------------------------|-------------------------------------------------------|


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
│  COMBINED STATE: [Z_out (Logic) | H_in (Raw Info)]            │
│  This is the input to the Output Stage.                       │
└───────────────────────────┬───────────────────────────────────┘
                            │
                            ▼
┌───────────────────────────────────────────────────────────────┐
│  PATH D: THE PRINTER (Output RNN)                             │
│                                                               │
│  At every time step t:                                        │
│  Input = [Previous Token] | [COMBINED STATE]                  │
│                                                               │
│  The RNN generates tokens using BOTH the new logic plan       │
│  AND the original raw context simultaneously.                 │
└───────────────────────────┬───────────────────────────────────┘
                            │
                            ▼
OUTPUT TEXT (Serial Stream)
