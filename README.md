# sensors-telemetry

Pet project: a simulated sensor sends temperature and humidity readings over MQTT
to a Spring Boot backend, which validates and stores them in PostgreSQL.

**Goal:** practice Spring Boot, JPA, SQL optimization (indexes, EXPLAIN ANALYZE),
fault tolerance and testing (JUnit 5, Mockito) on a realistic data flow.

## Architecture

Wokwi simulator -> MQTT broker -> Spring Boot service -> PostgreSQL

- **Device (Wokwi):** simulated sensor, publishes JSON messages
- **MQTT broker:** transport between device and backend
- **Backend (Spring Boot):** subscribes to the topic, validates, stores, exposes REST API
- **Database (PostgreSQL):** stores devices, sensors and measurements

## Message format

```json
{
  "timestamp": "2026-11-03T19:58:00Z",
  "deviceId": "123adzx",
  "sensorId": "01932",
  "data": {
    "temperature": 31.0,
    "humidity": 11
  }
}
```

- `timestamp`: ISO 8601, UTC
- `temperature`: number, °C
- `humidity`: number, %

## Validation rules

1. The message is valid JSON and contains all required fields
2. `timestamp` is a valid ISO 8601 UTC string
3. `temperature` is between -100 and 100 °C
4. `humidity` is between 0 and 100 %

## API

- `GET /sensors/{id}/temperature`
- `GET /sensors/{id}/humidity`

## Open decisions

- What happens to an invalid message (log and drop / error table / error topic)?
- Relation between `deviceId` and `sensorId`
- Which MQTT broker, and how Wokwi reaches it
- Query parameters for the API (`from`, `to`, pagination), latest value, aggregates

## Milestones (draft)

1. Repo + Spring Boot skeleton + PostgreSQL in Docker Compose. Result: app starts, connects to DB
2. Message DTO + validation. Result: unit tests cover every rule, valid and invalid cases
3. Persistence (entity, repository). Result: a valid message ends up in the DB
4. MQTT subscriber + broker. Result: a message published manually reaches the DB
5. Wokwi simulator publishes real messages. Result: end-to-end flow works
6. REST API with period filter. Result: integration test for each endpoint
7. Volume + SQL optimization. Result: 1M+ rows, EXPLAIN ANALYZE before/after an index
8. Fault tolerance (DB down, duplicates). Result: documented behavior + tests

## Status

Design phase.