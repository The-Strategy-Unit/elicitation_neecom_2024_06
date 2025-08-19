
<!-- README.md is generated from README.Rmd. Please edit that file -->

# NEECOM elicitation app

This repo contains the shiny app code for the National Elicitation
Exercise Community (NEECOM). The exercise was run online over a few
weeks in June 2024. Participants were recruited into two scenarios,
Steady State and Growing.

The aggregation and analysis of the results is on
[GitHub](https://github.com/The-Strategy-Unit/nhp_analysis/tree/5204e40ac00f53c4751ab1db42b2b1be0d061c06/2024-06-19-neecom-elicitation-runs).
The results are stored privately on
[Sharepoint](https://csucloudservices.sharepoint.com/sites/StrategyUnit384/Shared%20Documents/Forms/AllItems.aspx?id=%2Fsites%2FStrategyUnit384%2FShared%20Documents%2F1211%20NHP%20%2D%20Community%20model%20developments%2FNHSE%20Sprint%2FSprint%201%20%28June%2DJuly%2024%29%2FNEECom%2FResults&viewid=2509f8ca%2Da23c%2D4a6f%2D90fd%2D8dd6cf1564b4&FolderCTID=0x012000C7D125FAAA842344918923B668AB4376).
You can also use R to pull a results RDS file directly from Azure.

``` r
container_support <-
  su.azure::get_container(
    container_name = Sys.getenv("AZ_STORAGE_CONTAINER_SUPPORT")
  )

neecom_strategies <- AzureStor::storage_load_rds(
  container_support,
  "neecom_table.rds"
)
```

## Main changes from [core elicitation app](https://github.com/The-Strategy-Unit/nhp_elicitation_tool)

- The mitigator list was reduced to just a subset of mitigators relevant
  for the community setting. These community mitigators [were
  grouped](https://github.com/The-Strategy-Unit/elicitation_neecom_2024_06/commit/584df2fa96eba18589ec987d7765a3d36e2d3727)
  into “Public health”, “Ambulatory care and inpatient avoidance”,
  “Frail & Older people”, “Virtual Wards”.
- [Six new
  mitigators](https://github.com/The-Strategy-Unit/elicitation_neecom_2024_06/commit/0ceb32efd5f8e5bd7214fe2bf7590cffeb4242bd)
  which were not previously in the NEE exercise were included (four
  virtual wards mitigators and two general LOS.)
- Two scenarios were run; Scenario A (Steady state) and Scenario B
  (Growing). See text changes in [this
  commit](https://github.com/The-Strategy-Unit/nhp_elicitation_tool/commit/ea276cabf6c2d5492dbaf475ed23948f65fd485b).
  These were deployed as two seperate apps.
- Rationale was [collected as “MIRE” and
  low](https://github.com/The-Strategy-Unit/nhp_elicitation_tool/issues/18)
  rather than high / low. Because of this, it no longer made sense to
  present the high / low comments on the dumbbell plot in phase 2.*


* This made the hi/lo naming a [little confusing](https://github.com/The-Strategy-Unit/elicitation_neecom_2024_06/issues/2)

## Deployment

The script `deploy.R` can be used to redeploy to the production and
development environments.

The app is controlled by some environment variables that need to be set:

- `PHASE_1_END` should be the time and date when the first phase should
  end (in the form `YYYY-mm-dd HH:MM:SS` for the timezone
  `Europe/London`)
- `PHASE_2_END` should be as `PHASE_1_END`, but the time and date for
  when the second phase should end
- `PHASE_2_LIVE` should be set to a non-empty string to enable the
  second phase of the app: this allows the app to enter a “disabled”
  state at the end of phase 1
- `NHP_SALT` is a value that is used when encrypting the emails of users
- `save_path` should point to the location where to save the database
  to. If not set, then it defaults to the current working directory

## Development usage

The data required to run the app can be rebuilt using
`targets::tar_make()`. There are a few things that need to be set up in
order to get this to work.

1)  you will need to create a `.Renviron` file, with the following
    items:

- DB_SERVER=…
- DB_DATABASE=…
- NHP_SALT=…

2)  you will need to be connected to the MLCSU VPN and have access to
    the database.

3)  you will need a copy of the file `recruitment.xlsx`, stored in the
    root of the project folder.
