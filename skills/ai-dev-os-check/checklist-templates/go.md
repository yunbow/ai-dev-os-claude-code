<!-- This checklist is a standalone reference. No ai-dev-os-rules-go repository exists yet. -->
# Go Checklist

## Error Handling

- [ ] All error return values are checked
- [ ] Error wrapping (fmt.Errorf + %w) is appropriate
- [ ] Sentinel error usage is correct

## Concurrency

- [ ] No goroutine leaks
- [ ] Channels are properly closed
- [ ] sync.Mutex / sync.RWMutex usage is correct

## Security

- [ ] SQL injection prevention (placeholders) is in place
- [ ] User input is validated
- [ ] context.Context is properly propagated

## Code Quality

- [ ] Formatted with gofmt / goimports
- [ ] Exported types/functions have GoDoc comments
- [ ] Interfaces are kept small
