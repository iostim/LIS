# Installation

Copy `docker-compose.example.yml` to `docker-compose.override.yml` and change as needed.
Start with `docker-compose up -d`.

# Set up

- Application `app` container
- HAPIFHIR `hapi` container
- NGINX `nginx` container configured with three urls using environment variables:
  + /     pointing to `APP_URL`
  + /fhir pointing to `FHIR_URL`
  + /hapi pointing to `HAPI_URL`

To make it works, HAPI started inside `/hapi` context, adjust it if you change this endpoint in NGINX config.
Consequently you would need to adjust `HAPI_URL` and `FHIR_URL` since they are based on this context.

Applications are just JS modules.
They are served using webpack devserver on various ports.
Start the app(s) and adjust `APP_URL` to the root config or the application running in the standalone mode.

Vue applications require environment variable `VUE_APP_FHIR_URL` pointing to an `/fhir` endpoint of a FHIR service.

Also, each application is able to proxy `/fhir` requests to a FHIR service defined using `FHIR_TARGET` and `FHIR_PATH`.
Though `VUE_APP_FHIR_URL` still must be defined.

FIXME: Isn't the setup very hard, much complex, so overcomplicated?

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
find fhir -type f -print0 | xargs -0 -i curl -X POST -H "Content-Type: application/json" -d @{} -s -o /dev/null -w '%{http_code}\n' http://nginx:8080/fhir
```
