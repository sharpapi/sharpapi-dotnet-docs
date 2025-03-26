![SharpAPI GitHub cover](https://sharpapi.com/sharpapi-github-php-bg.jpg "SharpAPI .NET Client")

# SharpAPI .NET Client SDK Documentation

### 🚀 Automate Workflows with an AI-Powered API

Leverage the SharpAPI service in your .NET applications to streamline tasks in E-Commerce, Marketing, Content Management, HR Tech, Travel, and more.

[![Latest Version on NuGet](https://img.shields.io/nuget/v/SharpApi.Service.svg?style=flat-square)](https://www.nuget.org/packages/SharpApi.Service)
[![Total Downloads](https://img.shields.io/nuget/dt/SharpApi.Service.svg?style=flat-square)](https://www.nuget.org/packages/SharpApi.Service)

See more at [SharpAPI.com Website &raquo;](https://sharpapi.com/)

---

## Requirements

- .NET 5.0 (or later) / .NET Core 3.1+
- A valid SharpAPI API key – register at [SharpAPI.com](https://sharpapi.com/) to obtain one.

---

## ⛲ What Can It Do for You?

* **E-commerce**
  - Generate engaging product introductions.
  - Create personalized thank-you emails.
  - Streamline product categorization.
  - Analyze product review sentiments.

* **Content & Marketing Automation**
  - Translate text for a global audience.
  - Paraphrase, proofread, and summarize content.
  - Detect spam and extract contact information.
  - Generate unique keywords and SEO meta tags.

* **HR Tech**
  - Parse resumes (CV) from multiple file formats.
  - Generate detailed job descriptions.
  - Discover related skills and job positions.

* **Travel, Tourism & Hospitality**
  - Analyze travel review sentiments.
  - Categorize tours, activities, and hospitality products.

---

## Installation

You can install the package via NuGet. In your project directory, run:

```bash
dotnet add package SharpApi.Service
```

Alternatively, install using the NuGet Package Manager in Visual Studio.

---

## Setup & Configuration

### Dependency Injection

The SDK provides an extension method to easily register SharpAPI services into your dependency injection container. For example, in an ASP.NET Core application, update your `Program.cs` or `Startup.cs` as follows:

```csharp
using SharpApi.Service;

// Assuming you have configuration in appsettings.json under "SharpApi"
builder.Services.AddSharpApi(builder.Configuration.GetSection("SharpApi"));
```

### Configuration Settings

In your configuration file (e.g. `appsettings.json`), include a SharpAPI section similar to:

```json
{
  "SharpApi": {
    "ApiKey": "YOUR_SHARPAPI_API_KEY",
    "BaseAddress": "https://sharpapi.com",
    "MaximumDurationOfGettingResultInSeconds": 180,
    "DefaultHttpRetryAfterInSeconds": 10,
    "OverrideHttpRetryAfterWithClientDefault": false,
    "MaximumNumberOfHttpRedirections": 5,
    "ValidResumeFileExtensions": [ "pdf", "doc", "docx", "txt", "rtf" ]
  }
}
```

---

## Usage

The SDK exposes the `ISharpApiService` interface that provides asynchronous methods for calling the SharpAPI endpoints.

### Simple Example

Below is a basic example demonstrating how to call the `PingAsync` endpoint to check API availability:

```csharp
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using SharpApi.Service;
using System;
using System.Threading;
using System.Threading.Tasks;

public class Program
{
    public static async Task Main(string[] args)
    {
        // Build configuration and register services
        var configuration = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .Build();

        var services = new ServiceCollection();
        services.AddSharpApi(configuration.GetSection("SharpApi"));
        var serviceProvider = services.BuildServiceProvider();

        // Resolve the SharpAPI service
        var sharpApiService = serviceProvider.GetRequiredService<ISharpApiService>();

        // Example call to the Ping endpoint
        var pingResponse = await sharpApiService.PingAsync(CancellationToken.None);
        Console.WriteLine($"Ping: {pingResponse.Ping}, Timestamp: {pingResponse.Timestamp}");
    }
}
```

### Using Other API Methods

Each method dispatches a job request that returns a status URL. The SDK internally polls until the job status is `success` (or `failed` if an error occurred). Below are full code samples for all available endpoints:

---

## Code Samples for All API Endpoints

### General Endpoints

#### Ping
```csharp
var pingResponse = await sharpApiService.PingAsync(CancellationToken.None);
Console.WriteLine($"Ping: {pingResponse.Ping}, Timestamp: {pingResponse.Timestamp}");
```

#### Quota
```csharp
var quota = await sharpApiService.QuotaAsync(CancellationToken.None);
Console.WriteLine($"Subscription Words Used: {quota.SubscriptionWordsUsed} out of {quota.SubscriptionWordsQuota}");
```

---

### HR Endpoints

#### Parse Resume/CV
```csharp
var resume = await sharpApiService.ParseResumeAsync("path/to/resume.pdf", CancellationToken.None);
// Resume is returned as a structured object with details such as CandidateName, Positions, EducationQualifications, etc.
Console.WriteLine($"Candidate Name: {resume.CandidateName}");
```

#### Generate Job Description
```csharp
var jobRequest = new JobDescriptionRequest
{
    Name = "C# Senior Developer",
    CompanyName = "ACME Corp",
    MinimumWorkExperience = "5 years",
    MinimumEducation = "Bachelor's Degree",
    RequiredSkills = new[] { "C#", ".NET", "SQL" },
    OptionalSkills = new[] { "Azure", "Microservices" },
    Country = "United States",
    Remote = true,
    VisaSponsored = false,
    Language = Language.English,
    VoiceTone = "Professional",
    Context = "Detailed job role for senior level"
};

var jobDescription = await sharpApiService.JobDescriptionAsync(jobRequest, CancellationToken.None);
Console.WriteLine($"Job Short Description: {jobDescription.JobShortDescription}");
```

#### Related Skills
```csharp
var relatedSkills = await sharpApiService.RelatedSkillsAsync(
    new RelatedSkillsRequest { Content = "C#", Language = Language.English, MaxQuantity = 10 },
    CancellationToken.None
);

Console.WriteLine("Related Skills:");
foreach (var skill in relatedSkills.RelatedSkills)
{
    Console.WriteLine($"Skill: {skill.Name}, Weight: {skill.Weight}");
}
```

#### Related Job Positions
```csharp
var relatedJobs = await sharpApiService.RelatedJobPositionsAsync(
    new RelatedJobPositionsRequest { Content = "Senior Developer", Language = Language.English, MaxQuantity = 10 },
    CancellationToken.None
);

Console.WriteLine("Related Job Positions:");
foreach (var job in relatedJobs.RelatedJobPositions)
{
    Console.WriteLine($"Job Position: {job.Name}, Weight: {job.Weight}");
}
```

---

### E-commerce Endpoints

#### Product Review Sentiment Analysis
```csharp
var sentimentResult = await sharpApiService.ReviewSentimentAsync("The product is excellent, highly recommended!", CancellationToken.None);
Console.WriteLine($"Sentiment: {sentimentResult.opinion}, Score: {sentimentResult.score}");
```

#### Product Categories
```csharp
var categories = await sharpApiService.ProductCategoriesAsync(
    new ProductCategoriesRequest 
    { 
        Content = "Smartphone with great camera features", 
        Language = Language.English, 
        MaxQuantity = 5, 
        VoiceTone = "Neutral", 
        Context = "Electronics" 
    },
    CancellationToken.None
);

Console.WriteLine("Product Categories:");
foreach (var category in categories)
{
    Console.WriteLine($"Category: {category.Name}, Weight: {category.Weight}");
}
```

#### Product Introduction
```csharp
var productIntro = await sharpApiService.ProductIntroAsync(
    new ProductIntroRequest 
    { 
        Content = "This smartphone features a powerful camera and sleek design.", 
        Language = Language.English, 
        MaxLength = 300, 
        VoiceTone = "Energetic" 
    },
    CancellationToken.None
);
Console.WriteLine("Product Introduction: " + productIntro.ProductIntro);
```

#### Thank You Email Generation
```csharp
var thankYouEmail = await sharpApiService.ThankYouEmailAsync(
    new ThankYouEmailRequest 
    { 
        Content = "Thank you for your purchase!", 
        Language = Language.English, 
        VoiceTone = "Friendly", 
        Context = "Include a discount offer for next purchase" 
    },
    CancellationToken.None
);
Console.WriteLine("Thank You Email: " + thankYouEmail.email);
```

---

### Content & Marketing Automation Endpoints

#### Detect Phone Numbers
```csharp
var phoneNumbers = await sharpApiService.DetectPhoneNumbersAsync("Call us at 1800-394-7486 or +1-800-555-1234", CancellationToken.None);
foreach (var phone in phoneNumbers)
{
    Console.WriteLine($"Detected: {phone.DetectedNumber}, Parsed: {phone.ParsedNumber}");
}
```

#### Detect Emails
```csharp
var emails = await sharpApiService.DetectEmailsAsync("Contact us at info@example.com or support@example.org", CancellationToken.None);
Console.WriteLine("Detected Emails:");
foreach (var email in emails)
{
    Console.WriteLine(email);
}
```

#### Detect Spam
```csharp
var spamResult = await sharpApiService.DetectSpamAsync("This is a free money offer!", CancellationToken.None);
Console.WriteLine($"Spam Passed: {spamResult.Pass}, Score: {spamResult.Score}, Reason: {spamResult.Reason}");
```

#### Summarize Content
```csharp
var summary = await sharpApiService.SummarizeAsync(
    new SummarizeRequest 
    { 
        Content = "Long text content that needs summarizing goes here...", 
        Language = Language.English, 
        VoiceTone = "Neutral", 
        MaxLength = 150 
    },
    CancellationToken.None
);
Console.WriteLine("Summary: " + summary.Summary);
```

#### Generate Keywords/Tags
```csharp
var keywords = await sharpApiService.GenerateKeywordsAsync(
    new GenerateKeywordsRequest 
    { 
        Content = "Innovative and groundbreaking smartphone with advanced features.", 
        Language = Language.English, 
        MaxQuantity = 5, 
        VoiceTone = "Informative" 
    },
    CancellationToken.None
);
Console.WriteLine("Keywords: " + string.Join(", ", keywords));
```

#### Translate Text
```csharp
var translation = await sharpApiService.TranslateAsync(
    new TranslateRequest 
    { 
        Content = "Hello, how are you?", 
        Language = Language.French, 
        VoiceTone = "Neutral", 
        Context = "Greeting" 
    },
    CancellationToken.None
);
Console.WriteLine($"Translated Content: {translation.Content}, From: {translation.FromLanguage.Value}, To: {translation.ToLanguage.Value}");
```

#### Paraphrase Content
```csharp
var paraphrase = await sharpApiService.ParaphrazeAsync(
    new ParaphraseRequest 
    { 
        Content = "Original content that needs to be rephrased.", 
        MaxLength = 250, 
        Language = Language.English, 
        VoiceTone = "Formal", 
        Context = "Use synonyms where applicable" 
    },
    CancellationToken.None
);
Console.WriteLine("Paraphrased Text: " + paraphrase.Paraphrase);
```

#### Proofread Text
```csharp
var proofreadResult = await sharpApiService.ProofReadAsync("This are a sample text with error.", CancellationToken.None);
Console.WriteLine("Proofread Text: " + proofreadResult.Proofread);
```

---

### SEO Endpoints

#### Generate SEO Meta Tags
```csharp
var seoTags = await sharpApiService.GenerateSeoTagsAsync(
    new GenerateSeoTagsRequest 
    { 
        Content = "Content for generating SEO meta tags", 
        VoiceTone = "Professional" 
    },
    CancellationToken.None
);
Console.WriteLine("SEO Title: " + seoTags.MetaTags.Title);
```

---

### Travel, Tourism & Hospitality Endpoints

#### Travel Review Sentiment Analysis
```csharp
var travelSentiment = await sharpApiService.TravelReviewSentimentAsync("I loved the service and the ambiance was perfect.", CancellationToken.None);
Console.WriteLine($"Travel Sentiment: {travelSentiment.Opinion}, Score: {travelSentiment.Score}");
```

#### Tours & Activities Product Categories
```csharp
var taCategories = await sharpApiService.ToursAndActivitiesProductCategoriesAsync(
    new ToursAndActivitiesProductCategoriesRequest 
    { 
        Content = "Adventure tour in the mountains", 
        City = "Denver", 
        Country = "USA", 
        Language = Language.English, 
        MaxQuantity = 3, 
        VoiceTone = "Exciting", 
        Context = "Outdoor adventures" 
    },
    CancellationToken.None
);
Console.WriteLine("Tours & Activities Categories:");
foreach (var category in taCategories)
{
    Console.WriteLine($"Category: {category.Name}, Weight: {category.Weight}");
}
```

#### Hospitality Product Categories
```csharp
var hospitalityCategories = await sharpApiService.HospitalityProductCategoriesAsync(
    new HospitalityProductCategoriesRequest 
    { 
        Content = "Luxury hotel with spa and gourmet dining", 
        City = "Paris", 
        Country = "France", 
        Language = Language.English, 
        MaxQuantity = 3, 
        VoiceTone = "Elegant", 
        Context = "Luxury hospitality" 
    },
    CancellationToken.None
);
Console.WriteLine("Hospitality Categories:");
foreach (var category in hospitalityCategories)
{
    Console.WriteLine($"Category: {category.Name}, Weight: {category.Weight}");
}
```

---

## Error Handling

The SDK uses standard .NET exceptions (such as `HttpRequestException` and custom `SharpApiException`) to report errors. Make sure to catch these exceptions to handle API timeouts, validation errors, or unexpected response statuses gracefully.

---

## API Documentation

For detailed information on API endpoints, request parameters, and response formats, please refer to the official [API Documentation](https://sharpapi.com/documentation).

---

## Contributing

Contributions are welcome! If you encounter issues or have suggestions for improvements, please open an issue or submit a pull request on [GitHub](https://github.com/sharpapi/SharpApi.Service).

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

Happy coding with SharpAPI .NET Client SDK!
