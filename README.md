**Testing Power BI accessibility features**

Practical test plan for **Desktop → Service → embed**. Use keyboard + at least one screen reader.

---

### 1. What to test (feature map)

| Area | What Power BI offers | What you verify |
|------|----------------------|-----------------|
| **Alt text** | Per-visual alt text | Spoken / exposed description matches insight |
| **Tab order** | Selection pane order | Focus order is logical |
| **Keyboard nav** | Tab, arrows, Enter, Esc in reports | No traps; panes open/close |
| **Screen reader** | Names, roles, table data where exposed | Labels not “blank” / “chart” only |
| **Themes / contrast** | Report theme, colors | Text ≥ 4.5:1; not color-only meaning |
| **Data labels** | On charts | Values available without hover-only |
| **Table / matrix** | Native grid | Headers + values navigable |
| **Export** | Data / underlying data (where allowed) | Equivalent data outside visuals |
| **Embed** | iframe + host page | `title`, focus in/out, host alternatives |

---

### 2. Prepare a test report

Use a **small** report with:

1. One KPI card  
2. One bar or line chart (with **data labels** on)  
3. One **table** or matrix with the same metrics  
4. One **slicer** or filter  
5. Optional: bookmark or button  

In **Desktop**, for each visual:

- **Format → General → Properties → Alt text** (or current “Alt text” field)  
- Set a real description (metric + takeaway + “sandbox” if applicable)  
- **View → Selection** — order top-to-bottom = intended tab/read order  
- Turn **on data labels** for the chart  

Publish to **Service** (or use Desktop if you’re only testing authoring).

---

### 3. Desktop / Service — keyboard test

**Do not use the mouse.**

| Step | Action | Pass |
|------|--------|------|
| 1 | Open report | Focus visible on a control |
| 2 | **Tab** through slicer → KPI → chart → table | Order matches Selection pane / logic |
| 3 | **Shift+Tab** reverse | Returns without skip/jump chaos |
| 4 | Open filter pane (keyboard) | Can move through filters |
| 5 | **Esc** or Close | Pane closes; focus recoverable |
| 6 | On table: arrows / Tab as supported | Cells/headers announced or focus moves predictably |
| 7 | Activate button/bookmark if any | Works with Enter/Space |

**Fail:** focus disappears, cycles forever inside one visual, or cannot leave filter pane.

---

### 4. Screen reader test (Service or Desktop)

**Windows:** NVDA + Edge/Chrome  
**Mac:** VoiceOver + Safari  

| Step | Listen for | Fail if |
|------|------------|---------|
| Report loads | Page/report context | Silence or generic only |
| Each visual | **Alt text** or useful name | “Chart”, “Image”, “Blank” |
| Slicer | Name + value/state | “Combobox” with no name |
| Table | Column headers + values | Numbers without headers |
| After filter change | Updated context if available | No way to know data changed except visually |

Also open **Show data** / table view where the product allows and confirm the grid is usable with the SR.

---

### 5. Visual design checks (no SR required)

| Check | How |
|-------|-----|
| Contrast | Theme text/icons vs background; aim **4.5:1** for body text |
| Color-only | Remove color cues mentally: do labels/legend/patterns still work? |
| Zoom | Browser or OS zoom **200%** — labels not clipped, no essential hover-only info |
| Data labels | On for key series so values aren’t hover-only |

---

### 6. Embed-specific tests (host page)

Power BI features alone are not enough when embedded.

```html
<iframe
  title="Sandbox portfolio metrics — contributions by quarter"
  src="https://app.powerbi.com/view?r=..."
  ...
></iframe>
```

| Step | Pass criteria |
|------|----------------|
| iframe `title` | Descriptive, unique |
| Tab into iframe | Focus enters report |
| Tab out | Focus reaches host controls again (**no trap**) |
| Host **text summary** | Same insights without charts |
| Host **HTML table** or CSV link | Same data as visuals |
| Screen reader on host | Summary + table usable without using the iframe |

---

### 7. Feature deep-dives

**Alt text**  
- Write *what + so what*, not chart type only.  
- Re-test after changing the visual type (alt text can be cleared).

**Tab order**  
- Selection pane: drag order = tab order.  
- Hide decorative shapes from tab order if the product allows.

**Tables**  
- Prefer a real table visual for SR users.  
- Matrix: check header announcement depth (can be harder).

**Slicers**  
- Every slicer needs a visible title (becomes accessible name).  
- Test multi-select with keyboard only.

**Tooltips**  
- Hover tooltips are **not** enough; put critical values in labels, table, or alt text.

**Export**  
- If security allows, verify **Export data** and that a host-page CSV matches the report.

---

### 8. Scorecard (copy for QA)

| Feature | Desktop | Service | Embed + host | Notes |
|---------|---------|---------|--------------|-------|
| Alt text on all visuals | □ | □ | □ | |
| Logical tab order | □ | □ | □ | |
| Keyboard filter pane | □ | □ | □ | |
| No keyboard trap | □ | □ | □ | |
| Table equivalent | □ | □ | □ | |
| Contrast / not color-only | □ | □ | □ | |
| SR: named controls | □ | □ | □ | |
| Host summary + CSV | N/A | N/A | □ | |
| iframe title | N/A | N/A | □ | |

**Release rule:** any **Must-have** fail (trap, no non-visual equivalent, keyboard-blocked tasks) blocks “accessible” claims.

---

### 9. Known limits (set expectations)

- Interactive Power BI will not behave like a fully hand-coded WCAG page.  
- Compliance for most organizations = **report hardening + host page alternatives**.  
- “Publish to web” embeds are weaker for enterprise a11y/security than secure embed / app owns data.  
- Retest after **theme**, **visual type**, or **embed URL** changes.

---

### 10. Minimum 30-minute test

1. Alt text on every visual + table with same data (10 min)  
2. Keyboard-only pass including filter pane (10 min)  
3. NVDA/VoiceOver on Service + Tab in/out of embed (10 min)  

If those three pass and the host page has summary + table/CSV, you have a defensible baseline.

I can turn this into a one-page **QA script** (step → expected speech → pass/fail) tailored to Apex sandbox wording if you want that next.# Can I cache this?

This library tells when responses can be reused from a cache, taking into account [HTTP RFC 7234/9111](http://httpwg.org/specs/rfc9111.html) rules for user agents and shared caches.
It also implements `stale-if-error` and `stale-while-revalidate` from [RFC 5861](https://tools.ietf.org/html/rfc5861).
It's aware of many tricky details such as the `Vary` header, proxy revalidation, and authenticated responses.

## Basic Usage

`CachePolicy` is a metadata object that is meant to be stored in the cache, and it will keep track of cacheability of the response.

Cacheability of an HTTP response depends on how it was requested, so both `request` and `response` are required to create the policy.

```js
const policy = new CachePolicy(request, response, options);

if (!policy.storable()) {
    // throw the response away, it's not usable at all
    return;
}

// Cache the data AND the policy object in your cache
// (this is pseudocode, roll your own cache (lru-cache package works))
letsPretendThisIsSomeCache.set(
    request.url,
    { policy, body: response.body }, // you only need to store the response body. CachePolicy holds the headers.
    policy.timeToLive()
);
```

```js
// And later, when you receive a new request:
const { policy, body } = letsPretendThisIsSomeCache.get(newRequest.url);

// It's not enough that it exists in the cache, it has to match the new request, too:
if (policy && policy.satisfiesWithoutRevalidation(newRequest)) {
    // OK, the previous response can be used to respond to the `newRequest`.
    // Response headers have to be updated, e.g. to add Age and remove uncacheable headers.
    return {
        headers: policy.responseHeaders(),
        body,
    }
}

// Cache miss. See revalidationHeaders() and revalidatedPolicy() for advanced usage.
```

It may be surprising, but it's not enough for an HTTP response to be [fresh](#yo-fresh) to satisfy a request. It may need to match request headers specified in `Vary`. Even a matching fresh response may still not be usable if the new request restricted cacheability, etc.

The key method is `satisfiesWithoutRevalidation(newRequest)`, which checks whether the `newRequest` is compatible with the original request and whether all caching conditions are met.

### Constructor options

Request and response must have a `headers` property with all header names in lower case. `url`, `status` and `method` are optional (defaults are any URL, status `200`, and `GET` method).

```js
const request = {
    url: '/',
    method: 'GET',
    headers: {
        accept: '*/*',
    },
};

const response = {
    status: 200,
    headers: {
        'cache-control': 'public, max-age=7234',
    },
};

const options = {
    shared: true,
    cacheHeuristic: 0.1,
    immutableMinTimeToLive: 24 * 3600 * 1000, // 24h
    ignoreCargoCult: false,
};
```

If `options.shared` is `true` (default), then the response is evaluated from a perspective of a shared cache (i.e. `private` is not cacheable and `s-maxage` is respected). If `options.shared` is `false`, then the response is evaluated from a perspective of a single-user cache (i.e. `private` is cacheable and `s-maxage` is ignored). `shared: true` is recommended for HTTP clients.

`options.cacheHeuristic` is a fraction of response's age that is used as a fallback cache duration. The default is 0.1 (10%), e.g. if a file hasn't been modified for 100 days, it'll be cached for 100\*0.1 = 10 days.

`options.immutableMinTimeToLive` is a number of milliseconds to assume as the default time to cache responses with `Cache-Control: immutable`. Note that [per RFC](http://httpwg.org/http-extensions/immutable.html) these can become stale, so `max-age` still overrides the default.

If `options.ignoreCargoCult` is true, common anti-cache directives will be completely ignored if the non-standard `pre-check` and `post-check` directives are present. These two useless directives are most commonly found in bad StackOverflow answers and PHP's "session limiter" defaults.

### `storable()`

Returns `true` if the response can be stored in a cache. If it's `false` then you MUST NOT store either the request or the response.

### `satisfiesWithoutRevalidation(newRequest)`

Use this method to check whether the cached response is still fresh in the context of the new request.

If it returns `true`, then the given `request` matches the original response this cache policy has been created with, and the response can be reused without contacting the server. Note that the old response can't be returned without being updated, see `responseHeaders()`.

If it returns `false`, then the response may not be matching at all (e.g. it's for a different URL or method), or may require to be refreshed first (see `revalidationHeaders()`).

### `responseHeaders()`

Returns updated, filtered set of response headers to return to clients receiving the cached response. This function is necessary, because proxies MUST always remove hop-by-hop headers (such as `TE` and `Connection`) and update response's `Age` to avoid doubling cache time.

```js
cachedResponse.headers = cachePolicy.responseHeaders();
```

### `timeToLive()`

Suggests a time in _milliseconds_ for how long this cache entry may be useful. This is not freshness, so always check with `satisfiesWithoutRevalidation()`. This time may be longer than response's `max-age` to allow for `stale-if-error` and `stale-while-revalidate`.

After that time (when `timeToLive() <= 0`) the response may still be usable in certain cases, e.g. if client can explicitly allows stale responses.

### `toObject()`/`fromObject(json)`

You'll want to store the `CachePolicy` object along with the cached response. `obj = policy.toObject()` gives a plain JSON-serializable object. `policy = CachePolicy.fromObject(obj)` creates an instance from it.

## Complete Usage

### `evaluateRequest(newRequest)`

Returns an object telling what to do next — optional `revalidation`, and optional `response` from cache. Either one of these properties will be present. Both may be present at the same time.

```js
{
    // If defined, you must send a request to the server.
    revalidation: {
        headers: {}, // HTTP headers to use when sending the revalidation response
        // If true, you MUST wait for a response from the server before using the cache
        // If false, this is stale-while-revalidate. The cache is stale, but you can use it while you update it asynchronously.
        synchronous: bool,
    },
    // If defined, you can use this cached response.
    response: {
        headers: {}, // Updated cached HTTP headers you must use when responding to the client
    },
}
```

### Example

```js
let cached = cacheStorage.get(incomingRequest.url);

// Cache miss - make a request to the origin and cache it
if (!cached) {
    const newResponse = await makeRequest(incomingRequest);
    const policy = new CachePolicy(incomingRequest, newResponse);

    cacheStorage.set(
        incomingRequest.url,
        { policy, body: newResponse.body },
        policy.timeToLive()
    );

    return {
        // use responseHeaders() to remove hop-by-hop headers that should not be passed through proxies
        headers: policy.responseHeaders(),
        body: newResponse.body,
    }
}

// There's something cached, see if it's a hit
let { revalidation, response } = cached.policy.evaluateRequest(incomingRequest);

// Revalidation always goes first
if (revalidation) {
    // It's very important to update the request headers to make a correct revalidation request
    incomingRequest.headers = revalidation.headers; // Same as cached.policy.revalidationHeaders()

    // The cache may be updated immediately or in the background,
    // so use a Promise to optionally defer the update
    const updatedResponsePromise = makeRequest(incomingRequest).then(() => {
        // Refresh the old response with the new information, if applicable
        const { policy, modified } = cached.policy.revalidatedPolicy(incomingRequest, newResponse);

        const body = modified ? newResponse.body : cached.body;

        // Update the cache with the newer response
        if (policy.storable()) {
            cacheStorage.set(
                incomingRequest.url,
                { policy, body },
                policy.timeToLive()
            );
        }

        return {
            headers: policy.responseHeaders(), // these are from the new revalidated policy
            body,
        }
    });

    if (revalidation.synchronous) {
        // If synchronous, then you MUST get a reply from the server first
        return await updatedResponsePromise;
    }

    // If not synchronous, it can fall thru to returning the cached response,
    // while the request to the server is happening in the background.
}

return {
    headers: response.headers, // Same as cached.policy.responseHeaders()
    body: cached.body,
}
```

### Refreshing stale cache (revalidation)

When a cached response has expired, it can be made fresh again by making a request to the origin server. The server may respond with status 304 (Not Modified) without sending the response body again, saving bandwidth.

The following methods help perform the update efficiently and correctly.

#### `revalidationHeaders(newRequest)`

Returns updated, filtered set of request headers to send to the origin server to check if the cached response can be reused. These headers allow the origin server to return status 304 indicating the response is still fresh. All headers unrelated to caching are passed through as-is.

Use this method when updating cache from the origin server. Also available in `evaluateRequest(newRequest).revalidation.headers`.

```js
updateRequest.headers = cachePolicy.revalidationHeaders(updateRequest);
```

#### `revalidatedPolicy(revalidationRequest, revalidationResponse)`

Use this method to update the cache after receiving a new response from the origin server. It returns an object with two keys:

-   `policy` — A new `CachePolicy` with HTTP headers updated from `revalidationResponse`. You can always replace the old cached `CachePolicy` with the new one.
-   `modified` — Boolean indicating whether the response body has changed, and you should use the new response body sent by the server.
    -   If `true`, you should use the new response body, and you can replace the old cached response with the updated one.
    -   If `false`, then you should reuse the old cached response body. Either a valid 304 Not Modified response has been received, or an error happened and `stale-if-error` allows falling back to the cache.

# Yo, FRESH

![satisfiesWithoutRevalidation](fresh.jpg)

## Used by

-   [ImageOptim API](https://imageoptim.com/api), [make-fetch-happen](https://github.com/zkat/make-fetch-happen), [cacheable-request](https://www.npmjs.com/package/cacheable-request) ([got](https://www.npmjs.com/package/got)), [npm/registry-fetch](https://github.com/npm/registry-fetch), [etc.](https://github.com/kornelski/http-cache-semantics/network/dependents)
-   [Rust version of this library](https://lib.rs/crates/http-cache-semantics).

## Implemented

-   `Cache-Control` response header with all the quirks.
-   `Expires` with check for bad clocks.
-   `Pragma` response header.
-   `Age` response header.
-   `Vary` response header.
-   Default cacheability of statuses and methods.
-   Requests for stale data.
-   Filtering of hop-by-hop headers.
-   Basic revalidation request
-   `stale-if-error`
-   `stale-while-revalidate`

## Unimplemented

-   Merging of range requests, `If-Range` (but correctly supports them as non-cacheable)
-   Revalidation of multiple representations

### Trusting server `Date`

Per the RFC, the cache should take into account the time between server-supplied `Date` and the time it received the response. The RFC-mandated behavior creates two problems:

 * Servers with incorrectly set timezone may add several hours to cache age (or more, if the clock is completely wrong).
 * Even reasonably correct clocks may be off by a couple of seconds, breaking `max-age=1` trick (which is useful for reverse proxies on high-traffic servers).

Previous versions of this library had an option to ignore the server date if it was "too inaccurate". To support the `max-age=1` trick the library also has to ignore dates that pretty accurate. There's no point of having an option to trust dates that are only a bit inaccurate, so this library won't trust any server dates. `max-age` will be interpreted from the time the response has been received, not from when it has been sent. This will affect only [RFC 1149 networks](https://tools.ietf.org/html/rfc1149).
