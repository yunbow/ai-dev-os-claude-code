# Next.js Checklist

## Routing
- [ ] Follows App Router conventions (page.tsx, layout.tsx, loading.tsx)
- [ ] Dynamic route parameters have validation

## Server Components / Client Components
- [ ] "use client" usage is minimal
- [ ] Server Components do not use client-only APIs
- [ ] Client Components do not unnecessarily fetch server data

## Server Actions
- [ ] All Server Actions include auth check (auth())
- [ ] ActionResult pattern is used
- [ ] Validation (zod, etc.) runs at the top
- [ ] revalidatePath / revalidateTag are called appropriately

## Security
- [ ] Environment variables are server-only (no NEXT_PUBLIC_ prefix)
- [ ] User input is sanitized
- [ ] CSRF token verification is in place (where needed)

## Performance
- [ ] Images use next/image
- [ ] Suspense is used in appropriate places
- [ ] useMemo / useCallback prevent unnecessary re-renders
