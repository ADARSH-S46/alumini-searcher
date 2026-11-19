# Alumni LinkedIn Profile Searcher

Spring Boot app that searches LinkedIn for alumni of a university using the PhantomBuster API and saves the results in PostgreSQL.

## Stack
Java 17, Spring Boot 3.2, Spring Data JPA, PostgreSQL, springdoc (Swagger), JUnit 5 + Mockito

## Endpoints
| Method | URL | What it does |
|--------|-----|--------------|
| POST | `/api/alumni/search` | Searches LinkedIn, saves the profiles, returns them |
| GET | `/api/alumni/all` | Returns everything saved so far |

Swagger UI: `/swagger-ui.html`

### Sample request
```json
{
  "university": "University of XYZ",
  "designation": "Software Engineer",
  "passoutYear": 2020
}
```
`passoutYear` is optional.

## Running locally
1. Create a database: `createdb alumni_db`
2. Set these environment variables

```
DB_URL=jdbc:postgresql://localhost:5432/alumni_db
DB_USERNAME=postgres
DB_PASSWORD=postgres
PHANTOMBUSTER_API_KEY=...
PHANTOMBUSTER_AGENT_ID=...        # id of your LinkedIn Search Export phantom
LINKEDIN_SESSION_COOKIE=...       # li_at cookie of the LinkedIn account
```
3. `mvn spring-boot:run`
4. Tests: `mvn test`

## How it works
1. Controller validates the request and calls `AlumniService`.
2. The service asks a `ProfileScraper` for profiles. `PhantomBusterScraper` launches the agent, polls until it finishes, then reads the result.
3. Each profile is saved (or updated if the same name + university already exists) and returned.

## Notes
- The university and passout year come from the search input, since LinkedIn search results don't reliably expose them.
- If PhantomBuster fails or times out the API returns `502` with an error message.

## Deploying (Render)
- Create a PostgreSQL instance and a Web Service from this repo (Docker).
- Add the env vars above. Use the JDBC form of the connection string for `DB_URL`.
