# Nepal Transparency Engine Pilot

This is the backend service for the **Transparency Engine** and **Watchdog** system, built to run on the Nepal X-Road infrastructure.

## Prerequisites

- Java 17 or higher
- Gradle (Wrapper included)

## How to Run

1.  **Navigate to the directory:**
    ```bash
    cd nepal-pilot/transparency-engine
    ```

2.  **Run the application:**
    ```bash
    ./gradlew bootRun
    ```
    The application will start on `http://localhost:8080`.

## API Usage (Examples)

Since this service is designed to run behind an X-Road Security Server, it expects X-Road headers. You can simulate these locally using `curl`.

### 1. Allocate a Budget (Ministry of Finance)

```bash
curl -X POST "http://localhost:8080/api/v1/budget/allocate?year=2025&province=BAG&district=KAL&type=RD&sequence=001&projectName=Kalanki%20Road%20Paving&amount=10000000" \
     -H "X-Road-Client: NEP/GOV/MOF"
```

**Response:**
```json
{
  "trackingId": {
    "year": "2025",
    "province": "BAG",
    "district": "KAL",
    "type": "RD",
    "sequence": "001"
  },
  "projectName": "Kalanki Road Paving",
  "totalAmount": 10000000.00,
  "spentAmount": 0,
  "recipient": null,
  "frozen": false
}
```

### 2. Submit a Corruption Report (Citizen via Super App)

```bash
curl -X POST http://localhost:8080/api/v1/report \
     -H "Content-Type: application/json" \
     -H "X-Road-Client: NEP/GOV/SUPERAPP" \
     -H "X-Road-UserId: NID-1234567890" \
     -d '{
           "projectTid": "2025-BAG-KAL-RD-001",
           "userType": "ENGINEER",
           "location": {
             "latitude": 27.6710,
             "longitude": 85.3211,
             "ward": "Chandragiri-12"
           },
           "evidence": {
             "mediaHash": "sha256-hash...",
             "description": "Using sand instead of cement mix."
           },
           "whistleblowerHash": "user-hash-001"
         }'
```

### 3. Check Budget Status

```bash
curl -X GET "http://localhost:8080/api/v1/budget/2025-BAG-KAL-RD-001" \
     -H "X-Road-Client: NEP/GOV/PUBLIC"
```

## Testing

To run the automated tests (which verify the Consensus Algorithm and Auto-Freeze logic):

```bash
./gradlew test
```
