# synth-fraud-gen

A privacy-safe generator of synthetic payment transactions with configurable fraud patterns. Lets teams train, test and benchmark fraud detection models without touching real customer data, with built-in realism and leakage checks.

> **Status:** early development. The generator, fraud scenarios and quality checks are being built in the open. See the [Roadmap](#roadmap).

---

## Why this exists

Fraud detection models need transaction data to learn from, but real transaction data is some of the most sensitive data there is. As a result:

- **Students and researchers** have almost no realistic data to practise on.
- **Teams** cannot freely share data across departments, vendors or countries.
- **Public datasets** are few, often small, anonymized into unreadable numeric columns, and hard to adjust (you cannot ask for "more of this fraud type").
- **Fraud is rare**, usually well under 1% of transactions, so models need data with realistic imbalance and many labelled examples.

`synth-fraud-gen` aims to fix this by generating transaction data from scratch, so that **no real customer is ever involved**:

- **Realistic normal behaviour:** customers with spending habits, merchants, time-of-day patterns and locations.
- **Configurable fraud scenarios:** fraud types, rates and difficulty can be set by the user.
- **Reproducible output:** the same seed always gives the same dataset.
- **Quality checks:** measures of how realistic the data is, and checks that it does not copy real records.
- **Benchmark-ready labels:** every transaction has a known label and fraud type.

---

## What it generates

| Table | Contents |
|---|---|
| **Customers** | Synthetic customer profiles with spending level, home region and preferred merchant types |
| **Merchants** | Synthetic merchants with category, region and typical ticket size |
| **Transactions** | Time, amount, customer, merchant, channel (card present, online, mobile), location |
| **Labels** | Fraud or normal, plus the fraud scenario that created it |

All names, account numbers and card numbers are random placeholders. They are not derived from, and cannot be traced to, any real person or account.

### Planned fraud scenarios

- **Stolen credentials:** sudden purchases that do not match the customer's usual behaviour
- **Rapid-fire attempts:** many small transactions in a short window
- **Unusual location or channel:** activity far from the customer's normal region
- **Account takeover:** a change in behaviour after a quiet period
- **Merchant-side fraud:** unusual patterns concentrated at a few merchants

Scenarios are described at a behaviour level for training and testing detectors. The goal is to create labelled examples for defence, not to describe how to avoid detection.

---

## Planned approach

### Generation methods

- **Agent-based simulation:** each customer and merchant follows behaviour rules, so patterns emerge naturally
- **Statistical models:** amounts, timing and frequencies drawn from configurable distributions
- **Optional learned generators:** models fitted to public data (for example CTGAN-style) for comparison

### Quality and privacy checks

- **Realism:** compare distributions (amounts, hour of day, transactions per customer) against public datasets and known real-world patterns
- **Detector utility:** train a model on synthetic data and test it on a public fraud dataset to see how well it transfers
- **Privacy:** confirm no record is copied from any real source; report nearest-neighbour distance to the source data when learned generators are used
- **Leakage:** check that train and test splits do not share customers, so evaluation is honest

### Reference datasets for comparison

- PaySim (a public mobile-money transaction simulator)
- Credit card fraud dataset by ULB and Worldline (anonymized card transactions)
- IEEE-CIS Fraud Detection dataset

These are used only as references. They are not redistributed here, and each keeps its own license.

---

## How it works

```
config (customers, merchants, period, fraud rate, scenarios, seed)
                         |
                         v
        simulate normal customer and merchant behaviour
                         |
                         v
          inject labelled fraud scenarios
                         |
                         v
     transactions + labels (csv / parquet)
                         |
                         v
   quality report: realism, detector utility, privacy, leakage
```

---

## Planned repository structure

```
synth-fraud-gen/
├── configs/              # example configuration files
├── synth_fraud/
│   ├── customers.py      # customer profiles
│   ├── merchants.py      # merchant profiles
│   ├── simulate.py       # normal behaviour simulation
│   ├── scenarios/        # fraud scenarios
│   ├── quality/          # realism, privacy and leakage checks
│   └── export.py         # csv / parquet output
├── benchmarks/           # reference detector scripts and results
├── notebooks/            # exploration and plots
├── tests/
├── docs/
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

---

## Quickstart (planned)

```bash
git clone https://github.com/amoltathesoftwareengineer/synth-fraud-gen.git
cd synth-fraud-gen
pip install -r requirements.txt

# generate a dataset
python -m synth_fraud.generate --config configs/small.yaml --seed 42

# check the quality of the generated data
python -m synth_fraud.quality.report --data output/small

# train a reference detector on the synthetic data
python -m benchmarks.run_reference --data output/small
```

---

## Results

Results will be added once the first release is complete.

| Check | Setting | Score |
|---|---|---|
| Distribution similarity to public data | _coming soon_ | - |
| Detector trained on synthetic, tested on public data | _coming soon_ | - |
| Privacy (no record copying) | _coming soon_ | - |

---

## Roadmap

- [ ] Build customer and merchant profile generators
- [ ] Simulate normal transaction behaviour
- [ ] Add the first fraud scenarios with labels
- [ ] Add configuration files and seeded, reproducible output
- [ ] Build realism, privacy and leakage checks
- [ ] Add reference detectors and benchmark scripts
- [ ] Publish the first reproducible dataset and benchmark
- [ ] Add optional learned generators
- [ ] Add more channels and regions (including UPI-style payment patterns)

---

## Limitations and responsible use

- Synthetic data is only as realistic as the assumptions behind it. A detector that works well here may behave differently on real data. Always validate on real data inside a secure environment before any real-world use.
- The data is for **training, testing and education.** Do not use this project to design ways of evading fraud detection.
- Generated identifiers are random placeholders. Do not replace them with real card, account or personal numbers.
- This project is for research and education. It is not a certified tool for compliance or production fraud systems.

---

## Contributing

Contributions are welcome, especially:

- New realistic normal-behaviour models
- New labelled fraud scenarios for detector testing
- Better realism and privacy checks
- Bug reports and reproducibility fixes

Please open an issue to discuss before sending a large pull request. Do not include real transaction or personal data in any contribution.

---

## License

MIT License. See [LICENSE](LICENSE).

---

## Author

**Amol Tathe**
[LinkedIn](https://linkedin.com/in/amoltathesoftwareengineer) · [GitHub](https://github.com/amoltathesoftwareengineer)
