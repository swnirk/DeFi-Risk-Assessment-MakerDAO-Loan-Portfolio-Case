# DeFi Risk Assessment: MakerDAO Loan Portfolio Case

This study focuses on analyzing the risk associated with Maker’s loan portfolio by developing a risk model based on multiple Brownian motions passage levels with Brownian motions representing different collateral
types and levels being users’ collateralization ratios. Through numerical experiments using artificial and real data, we evaluate the model’s effectiveness in assessing risk within the loan portfolio.

In this repository you can find a calculation of metrics for comparing the mathematical models under consideration and calculating the joint probability of default of two different assets.

#### To obtain the main result of the research, the following steps must be taken:
- Getting data from [Google BigQuery](https://cloud.google.com/bigquery?hl=th) (dataset ID: bigquery-public-data.crypto_ethereum)
  ```
  SELECT
        block_timestamp,
        block_number,
        transaction_index,
        trace_address,
        transaction_hash,
        input,
        ARRAY_TO_STRING ( ARRAY( SELECT CHR(CAST(num AS INT64))
                                 FROM UNNEST(SPLIT(trace_address, ',')) AS num),
                          "," ) as trace_addr_str
  
  FROM  `bigquery-public-data.crypto_ethereum.traces`
  WHERE DATE(block_timestamp) >= "2019-11-12"
        and trace_address IS NOT NULL
        and call_type = "call"
        and to_address = LOWER("0x35D1b3F3D7966A1DFe207aa4514C12a259A0492B")
        and status = 1
  ORDER BY CAST(block_number as INT64), CAST(transaction_index as INT64), trace_addr_str
  ```
- ABI file is needed for MakerDAO data from smart contracts to decode a transaction. You can find required ABI file in [this site](https://ipfs.io/ipfs/bafybeid6dreur7nlcuqrdz7irtyffwvt3pvyzvac3cfdxmznw6kmoxmj3a)
- ```Data_decoding.ipynb``` is used to decode data from Google BigQuery
- ```Data_preparation_1.ipynb``` is used to split obtained after decoding table rows into such columns as users' ID, users' actions, timestamp, assets names, loan and collateral amounts
- ```Data_preparation_2.ipynb``` is used to define users' debt status at each timestamp and separate different debts of one user from each other
- ```Parameters_calculation.ipynb``` is used to map DAI/USD and ETH/DAI (WBTC/DAI) rates for each row in the final dataset and find liquidation rate and collateral ratio for every transaction in debt
- ```Interest_rate_decoding.ipynb``` is used to decode annual interest rate for each asset at each timestamp
- ```Metrics_calculation.ipynb``` is used to calculate matrics to compare Poisson and Brownian models using real data
- ```Joint_PD_calculation.ipynb``` is used to calculate joint probability of default of two different assets

#### Acknowledgements
Thanks to Yury Yanavich for supporting the research
