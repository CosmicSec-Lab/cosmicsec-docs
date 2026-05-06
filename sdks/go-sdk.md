# 🐹 Go SDK

## Overview

The **CosmicSec Go SDK** is a lightweight, idiomatic Go client for the CosmicSec platform. Includes retry logic with exponential backoff and context support.

**Location:** `cosmicsec-sdk/sdk/go/`  
**GoDoc:** `github.com/cosmicsec/cosmicsec-sdk/sdk/go`

## Installation

```bash
go get github.com/cosmicsec/cosmicsec-sdk/sdk/go
```

## Quick Start

```go
package main

import (
    "context"
    "fmt"
    "log"
    "time"
    
    "github.com/cosmicsec/cosmicsec-sdk/sdk/go/client"
)

func main() {
    // Initialize client
    c := client.NewClient(
        client.WithAPIKey("cs_live_12345..."),
        client.WithBaseURL("https://api.cosmicsec.com")),
        client.WithTimeout(30 * time.Second),
    )
    
    // Or login with credentials
    c, err := client.NewClient().Login(context.Background(), &client.LoginRequest{
        Email:    "user@company.com",
        Password: "SecureP@ssw0rd",
    })
    if err != nil {
        log.Fatal(err)
    }
    
    // Use the client
    scans, err := c.Scans().List(context.Background())
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("Total scans: %d\n", len(scans))
}
```

## Authentication

### API Key
```go
c := client.NewClient(
    client.WithAPIKey("cs_live_12345..."),
)
```

### Email/Password Login
```go
c, err := client.NewClient().Login(context.Background(), &client.LoginRequest{
    Email:    "user@company.com",
    Password: "SecureP@ssw0rd",
    TOTPCode: "123456",  // If 2FA enabled
})
```

## Scans

### List Scans
```go
// All scans
scans, err := c.Scans().List(context.Background())

// Filter by status
completedScans, err := c.Scans().List(context.Background(), client.WithStatus("completed"))

// Pagination
scans, err := c.Scans().List(context.Background(),
    client.WithLimit(10),
    client.WithOffset(0),
)
```

### Create Scan
```go
scan, err := c.Scans().Create(context.Background(), &client.CreateScanRequest{
    Target:   "example.com",
    ScanType: "web_app",
    Tools:    []string{"nmap", "nuclei", "nikto"},
    Options: map[string]interface{}{
        "ports":     "1-65535",
        "rateLimit": 100,
    },
})
if err != nil {
    log.Fatal(err)
}
fmt.Printf("Scan created: %s\n", scan.ScanID)
fmt.Printf("Status: %s\n", scan.Status)
```

### Get Scan
```go
scan, err := c.Scans().Get(context.Background(), "scan_12345")
if err != nil {
    log.Fatal(err)
}
fmt.Printf("Progress: %d%%\n", scan.Progress)
fmt.Printf("Vulnerabilities found: %d\n", scan.VulnerabilitiesCount)
```

### Cancel Scan
```go
err := c.Scans().Cancel(context.Background(), "scan_12345")
if err != nil {
    log.Fatal(err)
}
fmt.Println("Scan cancelled")
```

### Get Results
```go
results, err := c.Scans().GetResults(context.Background(), "scan_12345")
if err != nil {
    log.Fatal(err)
}
fmt.Printf("Found %d vulnerabilities\n", len(results.Vulnerabilities))

for _, vuln := range results.Vulnerabilities {
    fmt.Printf("[%s] %s\n", vuln.Severity, vuln.Title)
    fmt.Printf("  CVE: %s\n", vuln.CVE)
    fmt.Printf("  CVSS: %.1f\n", vuln.CVSSScore)
}
```

### Wait for Completion
```go
scan, err := c.Scans().Create(context.Background(), &client.CreateScanRequest{
    Target:   "example.com",
    ScanType: "network",
})
if err != nil {
    log.Fatal(err)
}

// Wait with timeout (1 hour) and polling interval (5 seconds)
completedScan, err := c.Scans().Wait(context.Background(), scan.ScanID,
    client.WithWaitTimeout(1*time.Hour),
    client.WithWaitInterval(5*time.Second),
)
if err != nil {
    log.Fatal(err)
}
fmt.Println("Scan completed!")
```

## AI Analysis

### Analyze Vulnerability
```go
analysis, err := c.AI().Analyze(context.Background(), &client.AnalyzeRequest{
    Content:       "SQL injection vulnerability in login form...",
    AnalysisType:  "vulnerability",
    Context:        map[string]interface{}{"target": "example.com"},
})
if err != nil {
    log.Fatal(err)
}
fmt.Printf("Severity: %s\n", analysis.Severity)
fmt.Printf("Summary: %s\n", analysis.Summary)
fmt.Println("Recommendations:")
for _, rec := range analysis.Recommendations {
    fmt.Printf("  - %s\n", rec)
}
```

### Multi-Model Ensemble
```go
consensus, err := c.AI().EnsembleAnalyze(context.Background(), &client.EnsembleRequest{
    Content:  "Analyze this critical vulnerability...",
    Models:   []string{"gpt-4o", "claude-3-opus", "llama-3.1-70b"},
    Strategy: "weighted_vote",
})
if err != nil {
    log.Fatal(err)
}
fmt.Printf("Consensus severity: %s\n", consensus.Consensus.Severity)
fmt.Printf("Confidence: %.2f\n", consensus.Consensus.Confidence)
```

## Reports

### Generate Report
```go
report, err := c.Reports().Generate(context.Background(), &client.GenerateReportRequest{
    ScanID:     "scan_12345",
    ReportType: "executive",  // executive, technical, compliance
    Format:     "pdf",        // pdf, html, json, csv
})
if err != nil {
    log.Fatal(err)
}
fmt.Printf("Report generated: %s\n", report.ReportID)
fmt.Printf("Download URL: %s\n", report.DownloadURL)
```

### List Reports
```go
reports, err := c.Reports().List(context.Background(), client.WithLimit(10))
if err != nil {
    log.Fatal(err)
}
for _, report := range reports {
    fmt.Printf("%s - %s\n", report.ReportID, report.CreatedAt)
}
```

### Download Report
```go
err := c.Reports().Download(context.Background(), "report_12345", "./reports/")
if err != nil {
    log.Fatal(err)
}
fmt.Println("Report downloaded")
```

## Error Handling

```go
import "github.com/cosmicsec/cosmicsec-sdk/sdk/go/errors"

resp, err := c.Scans().Get(context.Background(), "invalid_id")
if err != nil {
    if errors.IsNotFound(err) {
        fmt.Println("Scan not found")
    } else if errors.IsRateLimit(err) {
        retryAfter := errors.RateLimitRetryAfter(err)
        fmt.Printf("Rate limited. Retry after %d seconds\n", retryAfter)
    } else if errors.IsAuth(err) {
        fmt.Println("Invalid API key")
    } else {
        fmt.Printf("General error: %v\n", err)
    }
}
```

## Retry Logic

The SDK includes automatic retry with exponential backoff:

```go
c := client.NewClient(
    client.WithAPIKey("cs_live_..."),
    client.WithMaxRetries(3),           // Retry up to 3 times
    client.WithRetryDelay(1 * time.Second),  // Initial delay
    client.WithRetryBackoff(2.0),      // Exponential backoff multiplier
)
```

### Custom Retry Config
```go
type RetryConfig struct {
    MaxRetries  int
    InitialDelay time.Duration
    MaxDelay    time.Duration
    Backoff     float64
}

c := client.NewClient(
    client.WithRetryConfig(client.RetryConfig{
        MaxRetries:  5,
        InitialDelay: 500 * time.Millisecond,
        MaxDelay:     30 * time.Second,
        Backoff:      1.5,
    }),
)
```

## Context Support

All methods accept `context.Context` for cancellation and timeouts:

```go
// Create context with timeout
ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
defer cancel()

// Request will be cancelled after 30 seconds
scan, err := c.Scans().Create(ctx, &client.CreateScanRequest{...})
```

```go
// Cancellation via context
ctx, cancel := context.WithCancel(context.Background())

go func() {
    time.Sleep(5 * time.Second)
    cancel()  // Cancel after 5 seconds
}()

_, err := c.Scans().Create(ctx, &client.CreateScanRequest{...})
if err == context.Canceled {
    fmt.Println("Request cancelled")
}
```

## Configuration

### Client Options
```go
c := client.NewClient(
    client.WithAPIKey("cs_live_..."),
    client.WithBaseURL("https://api.cosmicsec.com"),
    client.WithTimeout(30 * time.Second),
    client.WithMaxRetries(3),
    client.WithRetryDelay(1 * time.Second),
    client.WithUserAgent("my-app/1.0"),
)
```

### Environment Variables
```bash
export COSMICSEC_API_KEY=cs_live_...
export COSMICSEC_BASE_URL=https://api.cosmicsec.com
export COSMICSEC_TIMEOUT=30000
export COSMICSEC_MAX_RETRIES=3
```

## Testing

```go
import (
    "testing"
    "github.com/cosmicsec/cosmicsec-sdk/sdk/go/client"
    "github.com/cosmicsec/cosmicsec-sdk/sdk/go/mocks"
)

func TestScanCreation(t *testing.T) {
    // Use mock client for testing
    mockClient := mocks.NewMockClient()
    mockClient.Scans().CreateFunc = func(ctx context.Context, req *client.CreateScanRequest) (*client.Scan, error) {
        return &client.Scan{
            ScanID: "scan_12345",
            Status: "queued",
        }, nil
    }
    
    scan, err := mockClient.Scans().Create(context.Background(), &client.CreateScanRequest{
        Target: "example.com",
    })
    
    if err != nil {
        t.Fatal(err)
    }
    
    if scan.ScanID != "scan_12345" {
        t.Errorf("expected scan_12345, got %s", scan.ScanID)
    }
}
```

## Next Steps

- [Python SDK](./python-sdk.md)
- [TypeScript SDK](./typescript-sdk.md)
- [JavaScript SDK](./js-sdk.md)
- [API Reference](../../dev/api-docs.md)
- [Examples on GitHub](https://github.com/cosmicsec/cosmicsec-sdk/examples)
