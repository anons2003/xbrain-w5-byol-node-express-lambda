# NOTES

## Strategy

I used `serverless-http`.

Changes:

- Added `lambda.js` as the Lambda entrypoint.
- Added the `serverless-http` npm dependency.
- Updated `template.yaml` from `Handler: TODO_FILL_IN` to `Handler: lambda.handler`.
- Kept `app.js` and `server.js` framework-pure so the app still runs locally with `npm start`.

## Reason

This is the smallest practical change for an existing Express app:

- No route rewrite.
- No Lambda-specific code inside `app.js`.
- No custom API Gateway event parsing.
- Local development stays unchanged through `server.js`.

I did not choose Lambda Web Adapter because it would avoid JavaScript changes but requires an extra Lambda layer/runtime boot path. For this assignment, a 3-line adapter entrypoint is simpler to review and debug.

## Deploy

- Region: `us-west-2`
- Stack: `byol-node-express`
- API Gateway URL: `https://4pahfvetw1.execute-api.us-west-2.amazonaws.com`

Smoke tests:

```bash
curl https://4pahfvetw1.execute-api.us-west-2.amazonaws.com/
curl https://4pahfvetw1.execute-api.us-west-2.amazonaws.com/api/hello/Lan
curl -X POST https://4pahfvetw1.execute-api.us-west-2.amazonaws.com/api/echo \
  -H 'Content-Type: application/json' \
  -d '{"hi":"there"}'
```

All three returned the same JSON shape as the local Express server.

## Cold Start

Measured from CloudWatch `REPORT` lines for `/aws/lambda/byol-node-express` after deploy:

- `Init Duration: 274.57 ms`
- `Init Duration: 333.75 ms`
- `Init Duration: 335.31 ms`

Reported cold start: about `315 ms` average.
