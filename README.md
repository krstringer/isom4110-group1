# HarbourTech Support Desk

ISOM4110 Group 1 · Topic 9 · OpenAI Agents SDK

HarbourTech is a fictional electronics store. This project uses the **OpenAI Agents SDK** to show how three agents handle policy questions and refund requests in Google Colab. Each notebook contains the code, eight synthetic products, ten orders and six store-policy clauses. All refund quotes and review tickets are simulated.

## Which notebook should I open?

| Notebook | Use it to |
|---|---|
| [Main support desk notebook](ISOM4110_Topic9_Support_Desk.ipynb) | Follow the implementation, run the offline tests, or try a conversation. Start here if you want to run the project yourself. |
| [Extended evaluation with saved outputs](ISOM4110_Topic9_Support_Desk_Extended_Evaluation.ipynb) | Inspect the demo and test results directly on GitHub without a key. This copy also includes two saved runs of an additional 11-case evaluation. |

## Run the main notebook in Google Colab

[Open the main notebook in Colab](https://colab.research.google.com/github/krstringer/isom4110-group1/blob/main/ISOM4110_Topic9_Support_Desk.ipynb). A standard CPU runtime is sufficient. Section 1 installs the pinned Python dependencies.

**To try the project without spending API credit**, edit Section 2 **before** choosing **Runtime → Run all**. The committed notebook has live mode enabled. Use these settings for an offline run:

```python
RUN_LIVE = False
RUN_LIVE_EVALUATION = False
```

Section 9 exercises the refund rules, safety checks and SDK handoffs using scripted model responses. Section 12.1 runs additional offline regression checks. Neither requires an API key.

**To try a live conversation**, add your personal HKUST GenAI subscription key to **Colab → Secrets** under the name `HKUST_API_KEY` and grant the notebook access. Use these settings in Section 2:

```python
RUN_LIVE = True
RUN_LIVE_EVALUATION = False
CREDENTIAL_SOURCE = "colab"
MODEL_ID = "gpt-4.1-mini"
```

Run through Section 10 to test the connection, then run Section 11 for the two-turn demo. To try your own request, enter it in the `YOUR_QUESTION` cell. For example: *“Please quote a refund for HT-1002.”*

If you already keep `HKUST_API_KEY` in a private `.env` file, set `CREDENTIAL_SOURCE` to `"local"` for a file accessible to the runtime, or `"drive"` for a file in your personal Google Drive. Set `ENV_PATH` to that file’s path. Each team member should use their own key and keep the `.env` file outside the repository.

## How the support desk works

Triage hands general questions to the **Policy agent**, which searches the six embedded clauses. Order-specific questions go to the **Refund agent**, which looks up the customer’s order, retrieves policy evidence and calls a Python function to calculate the quote.

For example, order `HT-1004` produces a simulated **HK$399** quote. Order `HT-1002` produces a **HK$1,299** quote, above the HK$500 review threshold. In that case, the Refund agent calls a fourth tool, `escalate_to_human`, to create a mock ticket pending review.

| Topic 9 dimension | What to look for |
|---|---|
| 1–2. Tool use and multi-step planning | Sections 6–7 define the tools and handoffs. Section 11 shows which agent finished and which tools it called. |
| 3. Memory and state | Sections 8 and 11 reuse one `SQLiteSession` so the customer can ask about “that same order” on the next turn. |
| 4. Retrieval-augmented generation | Sections 3–4 and 6 show how the policy search ranks the embedded clauses. In the saved quote, clause `P4` supports the explanation that shipping is excluded. |
| 5. Safety controls | Sections 5 and 11 show the input and output checks. In the saved demo, the output guardrail blocks a follow-up that cites a clause the agent did not retrieve on that turn. |
| 6–7. Developer experience and enterprise deployment fit | Section 13 discusses implementation choices and the limits of the Colab prototype. |

## Read the evaluation results

**Section 12** checks agent routing, required tool calls, decisions and amounts. In the saved run, **all five core cases passed**. Setting `INCLUDE_EXTRA_CASES = True` adds six optional cases to this evaluator.

The **extended notebook** also has a separate `TEST_CASES` suite immediately before Section 11. It runs independently of `INCLUDE_EXTRA_CASES`. Its first saved run recorded **7 passes, 1 prompt-injection attempt blocked as expected and 3 failures**. The second recorded **6 passes, 1 expected block and 4 failures**. The refund calculations use fixed Python rules. Live model responses and tool choices can vary between runs.

To view those results, open the saved notebook on GitHub. To rerun them, be aware that **Run all executes the additional 11-case suite twice**. The extended notebook sets a ceiling of **1,200 attempted model calls**, compared with **40** in the main notebook. These limits count calls, not tokens.

## Team workflow

Use a personal Colab copy for experiments. To contribute a change, save the intended `.ipynb` to a Git branch, review the changed cells and saved outputs for credentials or personal data, and open a pull request. Keep routine code changes in the main notebook and use the extended copy for results the team wants to share.