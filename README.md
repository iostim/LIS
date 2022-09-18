# Installation

Copy `docker-compose.example.yml` to `docker-compose.override.yml` and change as needed.
Start with `docker-compose up -d`.

# Synthetic data

Download synthetic data from [SyntheticMass](https://synthea.mitre.org/downloads).
You may want to keep random 50 (or any other number) records.

```
wget https://synthetichealth.github.io/synthea-sample-data/downloads/synthea_sample_data_fhir_r4_sep2019.zip
unzip -q synthea_sample_data_fhir_r4_sep2019.zip
find fhir -type f | grep -v "$(ls fhir | shuf -n 50)" | tr \\n \\0 | xargs -0 rm
```

Or generate with [Synthea](https://synthea.mitre.org/) yourself.

Upload the data to your FHIR server:

```
find fhir -type f -print0 | xargs -0 -i curl -X POST -H "Content-Type: application/json" -d @{} -s -o /dev/null -w '%{http_code}\n' http://127.0.0.1:8090/fhir
```
