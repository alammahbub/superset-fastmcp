# Superset MCP Server

**Repository**: [https://github.com/alammahbub/superset-fastmcp](https://github.com/alammahbub/superset-fastmcp)

The Superset MCP (Model Context Protocol) Server is a Python-based application that provides programmatic access to an Apache Superset instance. It enables AI assistants and agents (like Claude or Gemini) to natively interact with your Superset data stack—including dashboards, charts, databases, datasets, SQL Lab queries, user activities, tags, and more. It uses the `FastMCP` framework to manage tools and integrate with the platform's API over Server-Sent Events (SSE).

## Features

+ **Authentication**: Manage user authentication, token validation, token refreshing, and CSRF token management for safe API interaction.
+ **Dashboards**: List, retrieve, create, update, and delete dashboards.
+ **Charts**: Manage chart creation, updates, and deletions with support for various visualization types.
+ **Databases**: Handle database connections, including creation, testing, and schema/table retrieval.
+ **Datasets**: Create and manage datasets linked to database tables.
+ **SQL Lab**: Execute SQL queries, format queries, estimate query costs, and export results.
+ **Saved Queries**: Retrieve and create saved SQL queries.
+ **Query Management**: Stop running queries and retrieve query history.
+ **User Activity**: Access recent user activities and user role information.
+ **Tags**: Create, manage, and associate tags with platform objects.
+ **Exploration**: Create and retrieve form data and permalinks for chart exploration.
+ **Menu and Configuration**: Retrieve navigation menu data and platform API URL.
+ **Advanced Data Types**: Convert values to advanced data types and list available types.

## Installation

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/alammahbub/superset-fastmcp
   cd superset-fastmcp
   ```

2. **Install Dependencies**:
   Ensure you have Python 3.8+ installed. Create a virtual environment and install the required packages:
   ```bash
   python -m venv venv
   # On macOS/Linux: source venv/bin/activate
   # On Windows: venv\Scripts\activate
   pip install fastmcp python-dotenv httpx uvicorn
   ```

3. **Set Up Environment Variables**:
   Create a `.env` file in the project directory with the following:
   ```env
   ANALYTICS_API_URL=http://localhost:8088
   ANALYTICS_USER=admin
   ANALYTICS_PASS=admin
   ```
   - `ANALYTICS_API_URL`: The base URL of the Superset platform API.
   - `ANALYTICS_USER`: Your Superset platform username.
   - `ANALYTICS_PASS`: Your Superset platform password.

## Usage

### Starting the Server
Start the MCP server using the `main.py` script. By default, it runs on port 5008 to avoid collision with standard Superset ports.

```bash
python -m app.main
```
This starts the SSE endpoint at `http://localhost:5008/sse`.

### Configuring your AI Agent
To connect your AI assistant to this MCP server, add the SSE endpoint to your client's specific configuration file (e.g., `mcp_config.json`):

```json
{
  "mcpServers": {
    "superset": {
      "url": "http://localhost:5008/sse"
    }
  }
}
```

### Use in a Script
You can integrate the MCP server into another Python script using `fastmcp`:

```python
import asyncio
from app.main import mcp

async def main():
    # Use the FastMCP context to run a tool programmatically
    ctx = mcp.create_context() 
    result = await mcp.tools["analytics_auth_authenticate_user"](ctx, username="admin", password="password")
    print(result)

asyncio.run(main())
```

## Configuration

+ **Environment Variables**: Ensure the `.env` file is correctly configured with the Analytics platform's API URL and credentials.
+ **Port and Host**: Modify the `uvicorn.run` call in `app/main.py` to change the host or port if needed.
+ **Dependencies**: The server requires `fastmcp`, `uvicorn`, `python-dotenv`, and `httpx`. Install additional dependencies as needed for your environment.

## Contributing

Contributions are welcome! Please follow these steps:
1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/your-feature`).
3. Commit your changes (`git commit -m "Add your feature"`).
4. Push to the branch (`git push origin feature/your-feature`).
5. Open a pull request.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.

## Notes

+ The server assumes the Analytics platform API follows a structure similar to common BI platforms (like Apache Superset). If the API endpoints differ, update the endpoint paths in the respective tool files under `app/tools/`.
+ Make sure the Superset instance you are targeting has `WTF_CSRF_ENABLED = True` (or properly handles cookies if false), as this MCP server handles CSRF tokens explicitly.
+ For production use, secure the `.env` file and consider using a reverse proxy (e.g., Nginx) for the server.
