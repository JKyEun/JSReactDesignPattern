#### Promise.all

> react query에서 프로미스 병렬 처리 패턴인 Promise.all 쓰는 사례가 궁금해져서 확인해 봤습니다.

1. 쿼리 무효화

- invalidateQueries, resetQueries, refetchQueries 등 쿼리 무효화하는 메소드에서 Promise.all 사용하고 있더라구요

```javascript
// queryClent.ts
resetQueries(filters?: QueryFilters, options?: ResetOptions): Promise<void> {
    const queryCache = this.#queryCache

    const refetchFilters: RefetchQueryFilters = {
      type: 'active',
      ...filters,
    }

    return notifyManager.batch(() => {
      queryCache.findAll(filters).forEach((query) => {
        query.reset()
      })
      return this.refetchQueries(refetchFilters, options)
    })
  }

invalidateQueries(
    filters: InvalidateQueryFilters = {},
    options: InvalidateOptions = {},
  ): Promise<void> {
    return notifyManager.batch(() => {
      this.#queryCache.findAll(filters).forEach((query) => {
        query.invalidate()
      })

      if (filters.refetchType === 'none') {
        return Promise.resolve()
      }
      const refetchFilters: RefetchQueryFilters = {
        ...filters,
        type: filters.refetchType ?? filters.type ?? 'active',
      }
      return this.refetchQueries(refetchFilters, options)
    })
  }

refetchQueries(
    filters: RefetchQueryFilters = {},
    options?: RefetchOptions,
  ): Promise<void> {
    const fetchOptions = {
      ...options,
      cancelRefetch: options?.cancelRefetch ?? true,
    }
    const promises = notifyManager.batch(() =>
      this.#queryCache
        .findAll(filters)
        .filter((query) => !query.isDisabled())
        .map((query) => {
          let promise = query.fetch(undefined, fetchOptions)
          if (!fetchOptions.throwOnError) {
            promise = promise.catch(noop)
          }
          return query.state.fetchStatus === 'paused'
            ? Promise.resolve()
            : promise
        }),
    )

    return Promise.all(promises).then(noop)
  }
```

2. 쿼리 refecth 무효화

- cancelQueries 메소드에서도 사용하고 있었는데 해당 메소드는 쿼리 refecth를 취소하는 메소드 였습니다.

```javascript
// queryClent.ts

  cancelQueries(
    filters: QueryFilters = {},
    cancelOptions: CancelOptions = {},
  ): Promise<void> {
    const defaultedCancelOptions = { revert: true, ...cancelOptions }

    const promises = notifyManager.batch(() =>
      this.#queryCache
        .findAll(filters)
        .map((query) => query.cancel(defaultedCancelOptions)),
    )

    return Promise.all(promises).then(noop).catch(noop)
  }

```

> 그 외에는 useQueries, 네트워크 연결이 끊겨서 중지된 mutation을 다시 시작하는 resumePausedMutations 메소드에서도 사용하고 있었습니다.
