UXPilot Enterprise API
Programmatic access to AI-powered UX design tools for enterprise clients.

Base URL
All API requests should be made to the following base URL:

https://stream.uxpilot.ai
Authentication
Include API key in request headers.

Headers
http
Copy
x-api-key: ep_your_api_key_here
Content-Type: application/json
Alternative authentication header:

http
Copy
Authorization: Bearer ep_your_api_key_here
API Key Format
All enterprise API keys start with ep_ prefix
Example: ep_1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef
Available Endpoints
1. Unified Generation Endpoint - Generate wireframes, UI designs, or heatmaps

2. Flow-Based Generation - Generate multiple screens from user flows

3. Variation Flow Generation - Generate design variations

4. Get Design Status - Check status of a specific design

5. Batch Status Check - Check multiple designs at once

6. List All Designs - Get paginated list of all designs

7. Save Design for Figma - Mark a design as saved

8. Unsave Design for Figma - Remove saved status

9. Generate Screens Context - Generate screen flow suggestions (no credits)

1. Unified Generation Endpoint
Generate wireframes, UI designs, or heatmaps using a single endpoint.

POST
https://stream.uxpilot.ai/ep-api/generate
Request Body Format
All requests must include a type parameter to specify the generation method:

json
Copy
{
  "type": "wireframe|ui|heatmap",
  ...other_parameters
}
Response Strategy
Generation endpoints return minimal data by default (id, status, message)
Use responseFields parameter to request additional fields in the initial response
Use status endpoints to retrieve full results including HTML, preview URLs, and outputs
This reduces initial response size and bandwidth usage
Wireframe Generation
Type: wireframe | Permissions Required: wireframe

Request Body:

json
Copy
{
  "type": "wireframe",
  "topic": "E-commerce checkout page for fashion store",
  "selectedType": "desktop",
  "deepDesign": false,
  "editMode": false,
  "isNewScreen": false,
  "useMaxLimit": false,
  "wireframeId": "optional_existing_wireframe_id",
  "selectedSection": {},
  "attachments": {
    "attachedImages": []
  }
}
Parameter	Type	Required	Default	Description
type	string	Yes	-	Generation type: "wireframe"
topic	string	Yes	-	Description of what to wireframe
responseFields	array	No	[]	Additional fields to include in response
selectedType	string	No	"desktop"	Device type: "desktop", "mobile", "tablet"
temperature	number	No	0.4	AI creativity level (0.0-1.0)
deepDesign	boolean	No	true	Enable detailed design mode
editMode	boolean	No	false	Enable editing existing wireframe
isNewScreen	boolean	No	false	Create new screen in sequence
screenOrder	number	No	0	Screen order in flow
selectedModel	string	No	"model-5"	AI model to use
modelVariant	string	No	"default"	Model variant
useMaxLimit	boolean	No	true	Use maximum token limit
wireframeId	string	No	null	ID of existing wireframe to edit
selectedSection	object	No	{}	Section-specific - for editing specific section
attachments	object	No	{}	Attached images - public urls
Default Response:

json
Copy
{
  "id": "generated_wireframe_id",
  "status": "generating",
  "message": "Generation initiated successfully"
}
UI Design Generation
Type: ui | Permissions Required: ui_generate

Request Body:

json
Copy
{
  "type": "ui",
  "topic": "Modern dashboard for analytics platform",
  "wireframeId": "existing_wireframe_id",
  "designSystem": "ds-2",
  "selectedType": "desktop",
  "deepDesign": false,
  "editMode": false,
  "isNewScreen": false,
  "screenOrder": 0,
  "selectedSection": {},
  "customCollectionName": null,
  "modelVariant": "default",
  "useMaxLimit": false,
  "attachments": {
    "attachedImages": [],
    "attachedStickyNotes": []
  }
}
Parameter	Type	Required	Default	Description
type	string	Yes	-	Generation type: "ui"
topic	string	Yes	-	Design description or requirements
responseFields	array	No	[]	Additional fields to include in response
wireframeId	string	Yes	-	ID of wireframe to convert to UI
designSystem	string	No	"ds-2"	Design system: "ds-2"
selectedType	string	No	"desktop"	Device type: "desktop", "mobile", "tablet"
deepDesign	boolean	No	true	Enable detailed design mode
editMode	boolean	No	false	Edit existing design
isNewScreen	boolean	No	false	Create new screen
screenOrder	number	No	0	Screen order
selectedSection	object	No	{}	Section-specific settings
customCollectionName	string	No	null	Custom design collection
modelVariant	string	No	"default"	AI model variant
useMaxLimit	boolean	No	true	Use maximum tokens
attachments	object	No	{}	Attached resources
Default Response:

json
Copy
{
  "id": "generated_ui_id",
  "status": "generating",
  "message": "Generation initiated successfully"
}
Heatmap Generation
Type: heatmap | Permissions Required: heatmap

Request Body:

json
Copy
{
  "type": "heatmap",
  "imageUrl": "https://example.com/design-screenshot.png",
  "isSubscribed": true
}
Parameter	Type	Required	Default	Description
type	string	Yes	-	Generation type: "heatmap"
imageUrl	string	Yes	-	URL of design screenshot to analyze
isSubscribed	boolean	Yes	-	Subscription status (affects generating)
Response:

json
Copy
{
  "id": "heatmap_1640995200000",
  "status": "completed",
  "message": "Heatmap generated successfully",
  "url": "https://storage.googleapis.com/bucket/heatmap-result.png"
}
2. Flow-Based Generation
Generate multiple screens based on user flow description.

POST
https://stream.uxpilot.ai/ep-api/generate-by-flow
Permissions Required: ui_generate

Request Body:

json
Copy
{
  "prompt": "E-commerce mobile app for fashion store",
  "flows": [
    {
      "title": "Product Listing",
      "prompt": "Grid layout showing fashion products with filters",
      "position": { "x": 0, "y": 0 }
    },
    {
      "title": "Product Details",
      "prompt": "Detailed product view with size selection and add to cart",
      "position": { "x": 200, "y": 0 }
    },
    {
      "title": "Shopping Cart",
      "prompt": "Cart summary with quantity controls and checkout button",
      "position": { "x": 400, "y": 0 }
    }
  ],
  "selectedDesignMode": "wireframe",
  "selectedType": "mobile",
  "userInputPrompt": ["Create a modern fashion e-commerce app"],
  "contextCode": ""
}
Parameter	Type	Required	Default	Description
prompt	string	Yes	-	Overall application context
flows	array	No	-	Array of screen definitions
flows[].title	string	Yes	-	Screen title
flows[].prompt	string	Yes	-	Screen-specific requirements
flows[].position	object	No	-	Screen position in flow
selectedDesignMode	string	No	"wireframe"	Mode: "wireframe" or "ui"
selectedType	string	No	"desktop"	Device type
userInputPrompt	array	No	[]	Additional context prompts
contextCode	string	No	""	Existing code context
Response:

json
Copy
{
  "success": true,
  "designs": [
    {
      "title": "Product Listing",
      "design": {
        "id": "screen_1_id",
        "status": "generating"
      }
    },
    {
      "title": "Product Details",
      "design": {
        "id": "screen_2_id",
        "status": "generating"
      }
    }
  ],
  "message": "All screens initialized successfully"
}
3. Variation Flow Generation
Generate variations of existing designs.

POST
https://stream.uxpilot.ai/ep-api/generate-by-variation-flow
Permissions Required: ui_generate

Request Body:

json
Copy
{
  "flows": [
    {
      "title": "Homepage Variation 1",
      "prompt": "Minimalist approach with lots of white space",
      "position": { "x": 0, "y": 0 }
    },
    {
      "title": "Homepage Variation 2",
      "prompt": "Bold, colorful design with large hero image",
      "position": { "x": 200, "y": 0 }
    }
  ],
  "prompt": "Corporate website homepage",
  "selectedDesignMode": "ui",
  "selectedType": "desktop",
  "userInputPrompt": ["Create a professional corporate homepage"],
  "contextCode": "<html>...</html>"
}
Parameters: Same as /generate-by-flow

Response: Same as /generate-by-flow

4. Get Design Status
Check the status of a specific design by ID. Since design generation is asynchronous, use these endpoints to check the status and retrieve results.

GET
https://stream.uxpilot.ai/ep-api/status/{designId}
Permissions Required: Any valid API key (design must belong to API key user)

Parameter	Type	Required	Description
designId	string	Yes	ID returned from generation endpoint
Response:

json
Copy
{
  "status": "success",
  "data": {
    "id": "design_123",
    "status": "completed",
    "createdAt": "2024-01-15T10:30:00.000Z",
    "updatedAt": "2024-01-15T10:32:15.000Z",
    "prompt": "E-commerce checkout page",
    "designMode": "wireframe",
    "useCase": "desktop",
    "designSystem": "ds-2",
    "outputs": {
      "previewUrl": "https://storage.googleapis.com/bucket/preview.png",
      "htmlOutput": "<html>...</html>",
      "dimensions": { "width": 1200, "height": 800 }
    }
  }
}
Status Values:

generating / generating - Design is being created
completed - Design is ready with outputs
error / failed - Generation failed
unknown - Status unclear
5. Batch Status Check
Check status of multiple designs at once.

POST
https://stream.uxpilot.ai/ep-api/status/batch
Request Body:

json
Copy
{
  "designIds": ["design_123", "design_456", "design_789"]
}
Parameter	Type	Required	Description
designIds	array	Yes	Array of design IDs (max 30)
Response:

json
Copy
{
  "status": "success",
  "data": [
    {
      "id": "design_123",
      "status": "completed",
      "createdAt": "2024-01-15T10:30:00.000Z",
      "outputs": {
        "previewUrl": "https://storage.googleapis.com/bucket/preview.png",
        "htmlOutput": "<html>...</html>"
      }
    },
    {
      "id": "design_456",
      "status": "generating",
      "createdAt": "2024-01-15T10:35:00.000Z"
    },
    {
      "id": "design_789",
      "status": "not_found",
      "error": "Design not found or access denied"
    }
  ],
  "count": 3
}
6. List All Designs
Get a paginated list of all designs for your user with optional filtering.

GET
https://stream.uxpilot.ai/ep-api/status?status=completed&limit=20&offset=0
Parameter	Type	Required	Default	Description
status	string	No	-	Filter by status: "completed", "generating", etc.
designMode	string	No	-	Filter by mode: "wireframe", "ui", etc.
limit	number	No	50	Number of results (max 100)
offset	number	No	0	Pagination offset
sortBy	string	No	"createdAt"	Sort field
sortOrder	string	No	"desc"	Sort order: "asc" or "desc"
Response:

json
Copy
{
  "status": "success",
  "data": [
    {
      "id": "design_123",
      "status": "completed",
      "createdAt": "2024-01-15T10:30:00.000Z",
      "prompt": "Dashboard design",
      "designMode": "ui",
      "useCase": "desktop",
      "previewUrl": "https://storage.googleapis.com/bucket/preview.png"
    }
  ],
  "pagination": {
    "count": 20,
    "offset": 0,
    "limit": 50,
    "total": 150,
    "hasMore": true
  }
}
7. Save Design for Figma
Save a design/wireframe to mark it as saved in the user's library.

POST
https://stream.uxpilot.ai/ep-api/save-for-figma
Permissions Required: Any valid API key (design must belong to API key user)

Request Body:

json
Copy
{
  "wireframeId": "design_123"
}
Alternative - using designId:

json
Copy
{
  "designId": "design_123"
}
Parameter	Type	Required	Description
wireframeId	string	Yes*	ID of the design to save (*or use designId)
designId	string	Yes*	Alternative to wireframeId (*or use wireframeId)
Response:

json
Copy
{
  "status": "success",
  "message": "Design saved successfully",
  "designId": "design_123",
  "savedAt": "2024-01-15T10:30:00.000Z"
}
8. Unsave Design for Figma
Remove saved status from a design/wireframe.

DELETE
https://stream.uxpilot.ai/ep-api/save-for-figma
Permissions Required: Any valid API key (design must belong to API key user)

Request Body:

json
Copy
{
  "wireframeId": "design_123"
}
Parameter	Type	Required	Description
wireframeId	string	Yes*	ID of the design to unsave (*or use designId)
designId	string	Yes*	Alternative to wireframeId (*or use wireframeId)
Response:

json
Copy
{
  "status": "success",
  "message": "Design unsaved successfully",
  "designId": "design_123"
}
9. Generate Screens Context
Generate a flow of screens/pages based on a project description. This endpoint helps plan UI flows by suggesting screens needed for an application.

POST
https://stream.uxpilot.ai/ep-api/generate-screens-context
Permissions Required: Any valid API key

No credits charged - This is a planning/context generation endpoint

Request Body:

json
Copy
{
  "topic": "E-commerce platform for selling shoes",
  "pageContext": "Online marketplace for footwear with user accounts and shopping cart",
  "referenceCode": "<html>...</html>"
}
Parameter	Type	Required	Description
topic	string	Yes	Description of the application/website
pageContext	string	No	Additional context about the project
referenceCode	string	No	HTML code from existing page (for suggesting related flows)
Response:

json
Copy
{
  "screens": [
    {
      "title": "Signup",
      "description": "Signup screen with email and password input fields",
      "isMainScreen": false
    },
    {
      "title": "Homepage Dashboard",
      "description": "Homepage screen with featured products, deals, and categories",
      "isMainScreen": true
    },
    {
      "title": "Product Listings",
      "description": "Product listings page with filters and sorting",
      "isMainScreen": false
    },
    {
      "title": "Product Details",
      "description": "Product details page with information, reviews, and add-to-cart",
      "isMainScreen": false
    },
    {
      "title": "Shopping Cart",
      "description": "Cart view screen with quantity adjustment and item removal",
      "isMainScreen": false
    },
    {
      "title": "Checkout",
      "description": "Checkout screen with user authentication and payment information",
      "isMainScreen": false
    }
  ]
}
Response Fields:

Field	Type	Description
screens	array	Array of screen definitions
screens[].title	string	Screen/page title
screens[].description	string	Detailed description of the screen's content and purpose
screens[].isMainScreen	boolean	Whether this is the main/core screen of the application
Notes:

The isMainScreen flag identifies the core screen that will be the foundation/template for the app flow
When referenceCode is provided, the endpoint will suggest complementary screens (excluding the reference page itself)
Use the generated screens list to plan your design flow before using /ep-api/generate-by-flow
Error Responses
All endpoints return consistent error responses:

json
Copy
{
  "status": "error",
  "message": "Error description"
}
Common HTTP Status Codes:

Code	Description
200	Success
400	Bad Request - Invalid parameters
401	Unauthorized - Invalid or missing API key
403	Forbidden - Insufficient permissions or credits
402	Payment Required - Insufficient credits
429	Too Many Requests - Rate limit exceeded
500	Internal Server Error
Error Examples:

json
Copy
{
  "status": "error",
  "message": "API key does not have permission for wireframe service",
  "requiredPermission": "wireframe",
  "availablePermissions": ["ui_generate", "heatmap"]
}
json
Copy
{
  "status": "error",
  "message": "Insufficient credits",
  "required": 20,
  "available": 5
}
Rate Limiting
Default Limit: 100 requests per hour per API key
Rate Limit Headers: Included in responses
http
Copy
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1640995200
Version: 1.1

Last Updated: October 16, 2025
