Yandex images is the best
tineye
Use both screenshot approaches, not only full page, but elements as well
Check against our whitelist
use it as a way to ingest the data, not final reputation
we need to find a way to avoid a search loop
maybe curate the input data

I did a brainstorming session about this, and what I gathered:
```
Conclusion - Yandex images is the best, because Google is good at removing phishing and scam campaings from indexing.
Question - TinEye vs Yandex image API?
Question -  Should we take a screenshot of the whole page and send it to the image API or we should take specific images from the website and send them to the image API?
Conclusion - We should use it as a way to ingest new data instead of using it to assign a reputation. Our internal reputation checks will do the work, we just need the data we are missing and this could be a way to get it.
Question - How do we curate the input data? Meaning, we have a lot of traffic, hundreds of thousands of phishing/scam websites that are caught per day on our systems. How do we filter which websites to send, and what from the websites to send? Which images?
```