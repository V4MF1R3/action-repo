# GitHub Action Repository (action-repo)

This repository is configured to send webhook events to our webhook receiver for the following GitHub actions:
- **Push** events to any branch
- **Pull Request** events (opened, synchronized, reopened, closed)
- **Merge** events (when pull requests are merged)

## Repository Setup

### 1. Webhook Configuration

This repository has been configured with a webhook that sends events to the webhook receiver endpoint.

**Webhook Settings:**
- **Payload URL**: `https://your-webhook-receiver-domain.com/webhook`
- **Content Type**: `application/json`
- **Secret**: [Configured in repository settings]
- **SSL Verification**: Enabled
- **Events**: 
  - Push
  - Pull requests

### 2. Monitored Events

#### Push Events
Triggered when code is pushed to any branch in this repository.
- Captures: Author, target branch, timestamp, commits
- Display format: `{author} pushed to {branch} on {timestamp}`

#### Pull Request Events  
Triggered when pull requests are opened, updated, or reopened.
- Captures: Author, source branch, target branch, timestamp, PR details
- Display format: `{author} submitted a pull request from {from_branch} to {to_branch} on {timestamp}`

#### Merge Events
Triggered when pull requests are merged (closed with merged = true).
- Captures: Author, merged branches, timestamp, PR details  
- Display format: `{author} merged branch {from_branch} to {to_branch} on {timestamp}`

## Testing the Integration

### 1. Test Push Events
```bash
# Clone this repository
git clone <this-repo-url>
cd action-repo

# Make a test change
echo "Test change $(date)" >> test.txt
git add test.txt
git commit -m "Test push event"
git push origin main
```

### 2. Test Pull Request Events
```bash
# Create a new branch
git checkout -b feature/test-pr
echo "Feature change $(date)" >> feature.txt
git add feature.txt
git commit -m "Add feature change"
git push origin feature/test-pr

# Create a pull request through GitHub UI or GitHub CLI
gh pr create --title "Test PR" --body "Testing pull request webhooks"
```

### 3. Test Merge Events
1. Create a pull request (as above)
2. Review and merge the pull request through GitHub UI
3. Check the webhook receiver for the merge event

## Webhook Payload Examples

### Push Event Payload
```json
{
  "ref": "refs/heads/main",
  "before": "abc123...",
  "after": "def456...",
  "pusher": {
    "name": "john_doe",
    "email": "john@example.com"
  },
  "head_commit": {
    "id": "def456...",
    "message": "Add new feature",
    "timestamp": "2024-01-15T10:30:00Z",
    "author": {
      "name": "John Doe",
      "email": "john@example.com"
    }
  },
  "commits": [...],
  "repository": {
    "name": "action-repo",
    "full_name": "username/action-repo"
  }
}
```

### Pull Request Event Payload
```json
{
  "action": "opened",
  "pull_request": {
    "number": 42,
    "title": "Add new feature",
    "user": {
      "login": "jane_doe"
    },
    "head": {
      "ref": "feature/new-feature"
    },
    "base": {
      "ref": "main"
    },
    "created_at": "2024-01-15T09:00:00Z",
    "merged": false
  },
  "repository": {
    "name": "action-repo",
    "full_name": "username/action-repo"
  }
}
```

### Merge Event Payload
```json
{
  "action": "closed",
  "pull_request": {
    "number": 42,
    "title": "Add new feature",
    "merged": true,
    "merged_at": "2024-01-15T14:00:00Z",
    "merged_by": {
      "login": "bob_smith"
    },
    "head": {
      "ref": "feature/new-feature"
    },
    "base": {
      "ref": "main"
    }
  },
  "repository": {
    "name": "action-repo",
    "full_name": "username/action-repo"
  }
}
```

## Verification

After setting up both repositories and the webhook, you can verify the integration by:

1. **Making test commits** to trigger push events
2. **Creating pull requests** to trigger PR events  
3. **Merging pull requests** to trigger merge events
4. **Checking the webhook receiver UI** at your deployed endpoint
5. **Monitoring webhook delivery** in GitHub repository settings

## Branch Strategy

This repository uses the following branch strategy for testing:

- `main` - Main development branch
- `staging` - Staging branch for testing
- `feature/*` - Feature branches for pull requests
- `hotfix/*` - Hotfix branches for urgent fixes

## Sample Files

This repository contains sample files to demonstrate different types of changes:

- `README.md` - This documentation
- `src/` - Sample source code directory
- `tests/` - Sample test files
- `docs/` - Documentation files
- `.github/` - GitHub configuration (if needed)

## Webhook Delivery Monitoring

You can monitor webhook deliveries in your GitHub repository settings:

1. Go to repository **Settings**
2. Click on **Webhooks** 
3. Click on your webhook URL
4. View the **Recent Deliveries** section
5. Check delivery status and response codes

## Troubleshooting

### Webhook Not Firing
- Check repository webhook settings
- Verify webhook URL is accessible
- Check webhook secret configuration
- Review recent deliveries for error messages

### Events Not Appearing in UI
- Verify webhook receiver is running
- Check webhook receiver logs
- Confirm MongoDB is connected
- Test API endpoints directly

## Related Repositories

- **webhook-repo**: Flask application that receives and processes webhooks
- **action-repo**: This repository configured to send webhook events

## Contributing

To contribute to this test repository:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Create a pull request
5. Verify webhook events are triggered correctly

This repository serves as a demonstration of GitHub webhook integration and helps test the webhook receiver functionality.