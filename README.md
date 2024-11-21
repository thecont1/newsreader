# Generating a News and Weather Report using APIs

### Credits

Because it's due right upfront. This newsreader project is the outcome of a [code-along webinar](https://www.youtube.com/watch?v=AvgLXgELxuM) taught by [Chris Ramakers](https://github.com/chrisramakers) of [DataCamp](https://www.datacamp.com/). [My notebook](newsreader-thecont1.ipynb) is a heavily modified version of the Datacamp notebook that accompanies Ramaker's tutorial. The original can be found [here](https://bit.ly/40ShyEk) (signup required) or an unmodified copy [here](newsreader-datacamp.ipynb) in this repository.

### Objective
Using Python to chain together several APIs, create a an agentic news editor – modelled on a famous personality – who writes a commentary on the news and weather forecast in your location. The program ends by generating an MP3 file of the bulletin being read out to an audience.

### The Plan
- Task 0: Prepare your work environment

- Task 1: Get news headlines from GNews

- Task 2: Fetch weather forecast from WeatherAPI

- Task 3: Generate the news bulletin text using OpenAI chat completions

- Task 4: Generate an audio file using OpenAI Text-to-Speech 

### Sources
To eventually construct our sound clip, we will pull the requisite data from various sources. Check out their API documentation to know more about how to use them:

1. [GNews API](https://gnews.io/docs/v4?python#introduction) 

2. [WeatherAPI](https://www.weatherapi.com/docs/)

3. [OpenAI chat completions API](https://platform.openai.com/docs/guides/text-generation)

4. [OpenAI text-to-speech API](https://platform.openai.com/docs/guides/text-to-speech)

### A quick refresher on API concepts

Before we get started let's refresh a few important API concepts. If you've already worked with APIs before, or if you completed the [Introduction to APIs in Python course](https://www.datacamp.com/courses/introduction-to-apis-in-python), none of this should be new. For those of you who have never worked with APIs before or have limited experience, this is a useful refresher and will make following along with the rest of the code-along a lot easier.

<details>
  <summary>What is an API?</summary> 
  <p>

APIs, or Application Programming Interfaces, are like the messengers of the digital world, making sure apps, devices, and services can talk to each other smoothly. They let different systems share data and perform actions without getting tangled in each other’s code. You might not notice it, but APIs are behind almost everything we do digitally. Plug in your electric car, and an API’s handling the data exchange with the charger. Check the weather or get an email on your phone? That’s thanks to APIs pulling and delivering data from online sources to your device.

In essence, APIs make life easier for both developers and users, setting the rules for how apps communicate and what they can share. Whether it’s streaming music, ordering food, or tracking fitness, APIs keep it all connected. They’re one of the most powerful, behind-the-scenes technologies that make our digital world feel effortless, despite being complex and varied under the hood.
   </p>
</details>

<details>
  <summary>REST APIs</summary> 
  <p>

APIs come in many different styles and flavors, but for the purpose of this code-along, we will be focusing on REST APIs. REST APIs let two systems communicate over the internet, using HTTP, the same protocol your browser uses to load webpages. Think of it like typing a website address into your browser: you send a request, and the page loads in response. 

With rest APIs, the system that sends the request is called **the client**, the system the receives the request and returns a response is usually **the server**. 

![Request response cycle](resources/request-response-cycle.png)
   </p>
</details>


<details>
  <summary>HTTP Verbs</summary> 
  <p>

REST APIs are based on HTTP verbs. These describe the action the client wishes to perform on a resource on server. In total there are 9 HTTP verbs, in practice you'll be using the 4 most common verbs described in the table below.

| Verb   | Action |
|-|-|
| `GET`    | Reading a resource |
| `POST`   | Create a new resource |
| `PUT`    | Update an existing resource |
| `DELETE` | Delete a resource |
   </p>
</details>


<details>
  <summary>URLs and parameters</summary> 
  <p>

![Structure of URLs](resources/url-structure.png)

A REST API is accessible over the internet through a URL, which acts like an address that points to a specific resource on the server. This URL includes several parts, as shown in the image: the protocol (http://), the server’s domain (350.5th-ave.com), the port number (like :80), the path to the specific resource (e.g., /unit/243), and optional query parameters (e.g., ?floor=77).

When interacting with REST APIs, you’ll often need to include additional information, like filters or specific search terms, to get exactly the data you want. This extra information is usually passed as query parameters in the URL. These parameters help refine your request to retrieve more specific data, making the API flexible and responsive to a wide range of needs.
   </p>
</details>


<details>
  <summary>Headers</summary> 
  <p>

Headers carry additional information about the request or response, providing context for how the message should be processed. They can specify how the authenticate or what the content type is that's being sent. Headers essentially help the server and client understand the message’s format and how to handle it. Each header is a key-value pair, written as `Key: Value`, where the key is case-insensitive.
   </p>
</details>


<details>
  <summary>Response status codes</summary> 
  <p>

There are over 70 HTTP status codes, grouped into five main categories. These categories help us quickly understand the nature of the response. Codes starting with 1XX are informational, 2XX indicate success, 3XX handle redirections, 4XX signal client-side errors, and 5XX point to server-side issues.

Among these, three key codes to remember are:
- `200` **OK**: The request was successful, and the server returned the expected data.
- `404` **Not Found**: The server couldn’t find the requested resource.
- `500` **Internal Server Error**: Something went wrong on the server’s side, causing the request to fail.

   </p>
</details>