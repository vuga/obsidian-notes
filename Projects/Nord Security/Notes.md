### Old general prompt:
GENERAL_PROMPT = """

You are an advanced language model tasked with categorizing website content to determine if it corresponds to one of the following categories: Clean, Phishing, Malware, Unsure. You will analyze the text found on the website, considering various factors such as keywords, context, and overall theme.

  

1. Assess the content for relevant keywords and phrases associated with brand impersonation, malicious files, calls to action, creation of urgency, deception.

2. Consider the context in which these keywords appear and how they relate to the overall theme of the website.

3. Identify the language of the content and ensure that your analysis is language-agnostic, enabling you to recognize equivalent terms in various languages that pertain to cybersecurity threats.

4. Provide a clear categorization result: "Clean" if the website fits within the Clean category, "Phishing" if it is attempting to deceive users into providing sensitive information, "Malware" if it is distributing malicious software, and "Unsure" if it is difficult to determine.

5. If the website content includes significant elements related to government functions in a non-English language, translate key sections or keywords to ensure accurate categorization.

6. Check for edge-cases, for example, inappropriate content, related to violence or pornography is not necessarily phishing or malware.

  

Example input: "A website that uses assets to look like Facebook and collects user credentials."

  

Expected output: "Phishing"

  

Input: "{website_content}"

  

Output:

"""

### Old script
def get_headers():

    nexosai_api_key = os.environ.get("NEXOS_API_KEY")

    if not nexosai_api_key:

        raise ValueError("NEXOS_API_KEY environment variable is not set.")

    return {"Authorization": f"Bearer {nexosai_api_key}", "Content-Type": "application/json"}

  
  

async def send_request_async(api_url: str, timeout: int = 1000, **kwargs):

    headers = get_headers()

  

    async with aiohttp.ClientSession(headers=headers) as session:

        try:

            async with session.post(url=api_url, timeout=timeout, **kwargs) as response:

                response_json = await response.json()

                return response_json

        except Exception as e:

            print(f"Request failed: {str(e)}")

            return None

  
  

async def call_model_on_prompt_async(

    prompt: str,

    model: str,

    api_url: str = "https://api.nexos.ai/v1/chat/completions",

    model_params: dict[Any] | None = None,

    timeout: int = 100,

) -> str:

  

    default_params = {

        "store": False,

        "frequency_penalty": 0,

        "max_tokens": 0,

        "max_completion_tokens": 10,

        "n": 1,

        "presence_penalty": 0,

        "temperature": 1,

        "top_p": 1,

    }

    if model_params:

        default_params.update(model_params)

  

    payload = {

        "messages": [{"role": "user", "content": prompt}],

        "model": model,

    }

    payload.update(default_params)

  

    model_answer = await send_request_async(api_url=api_url, timeout=timeout, json=payload)

    try:

        return model_answer["choices"][0]["message"]["content"]

    except Exception as e:

        return "model failed to answer"

  
  

async def process_url(

    url: str,

    browser: Browser,

    model_id: str,

    prompt: str = GENERAL_PROMPT,

):

    html_raw = await get_html_content(url=url, browser=browser)  # Fixed URL parameter

    html_cleaned = get_html_text_cleaned(html=html_raw)

    prompt_formatted = prompt.format(website_content=html_cleaned)

    llm_response = await call_model_on_prompt_async(prompt=prompt_formatted, model=model_id)

    return llm_response

  
  

async def process_all_urls(data, model_id):

    results = []

  

    with tqdm(total=len(data), desc="Processing URLs") as pbar:

        for _, row in data.iterrows():

            async with browser_manager() as browser:

                try:

                    response = await process_url(url=row["url"], browser=browser, model_id=model_id)

                    results.append((row["url"], response))

                except Exception as e:

                    print(f"Error processing {row['url']}: {str(e)}")

                    results.append((row["url"], f"Error: {str(e)}"))

                finally:

                    pbar.update(1)

  

            await asyncio.sleep(0.5)

  

    results_df = pd.DataFrame(results, columns=["url", "llm_response"])

    return results_df


## FP review script
1/1 wetransfer phishing
2/2 now white page, but seems like hosted phishing
2/3 did not catch parked domain
3/4 clean parked domain