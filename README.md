# SonarQube MCP Server Integration

This repository contains a complete setup for running SonarQube Community Edition with the official SonarSource MCP Server integration for Windsurf.

## 🏗️ Architecture

- **SonarQube Community Edition** - Running in Docker at `localhost:9000`
- **PostgreSQL Database** - Backend storage for SonarQube
- **Official SonarSource MCP Server** - Built locally (Java 21)
- **Windsurf Integration** - Via MCP protocol for code analysis

## 🚀 Quick Start

### 1. Start SonarQube

```bash
docker-compose up -d
```

Wait for SonarQube to be ready:
```bash
curl -s http://localhost:9000/api/system/ping
# Should return: pong
```

### 2. Configure Windsurf MCP

The MCP server is already built and configured. Import the MCP configuration:

**File:** `mcp-config.json`
```json
{
  "mcpServers": {
    "sonarqube": {
      "command": "/opt/homebrew/opt/openjdk@21/bin/java",
      "args": [
        "-jar",
        "/Users/taylorcurran/Documents/dev/run-sonarqube/sonarqube-mcp-server/build/libs/sonarqube-mcp-server-0.0.3-SNAPSHOT.jar"
      ],
      "env": {
        "JAVA_HOME": "/opt/homebrew/opt/openjdk@21",
        "STORAGE_PATH": "/tmp/mcp-sonarqube-storage",
        "SONARQUBE_TOKEN": "squ_REDACTED",
        "SONARQUBE_URL": "http://localhost:9000"
      }
    }
  }
}
```

### 3. Refresh MCP in Windsurf

1. Open Windsurf
2. Go to MCP settings
3. Click the refresh button
4. The SonarQube MCP server should connect successfully

## 🚀 Quick Demo

To run a complete demo from a fresh computer restart:

### Step 1: Start Docker Desktop
```bash
# On macOS - opens Docker Desktop application
open -a Docker

# Wait for Docker to fully start (check with)
docker info
```

### Step 2: Start SonarQube & MCP Server
```bash
# Navigate to project directory
cd /Users/taylorcurran/Documents/dev/run-sonarqube

# Start all services (SonarQube + PostgreSQL + MCP containers)
docker-compose up -d

# Verify services are running
docker-compose ps
```
You may need to wait a few seconds for the SonarQube container to start up.

### Step 3: Verify Everything is Working
```bash
# Test SonarQube is responding
curl -s http://localhost:9000/api/system/ping
# Should return: "pong"

# In Windsurf, test MCP connection:
# "Can you check SonarQube system health?"
# Should return: "SonarQube Server Health Status: GREEN"
```

### Step 4: Ready for Analysis!
```
# Now you can use commands like:
- "Analyze this code snippet for issues"
- "Show me my SonarQube projects"
- "Get system health status"
- "Search for issues in my projects"
```

**Expected Timeline:**
- Docker startup: ~30 seconds
- SonarQube initialization: ~1-2 minutes
- MCP server auto-starts with Windsurf
- Total setup time: ~3 minutes

## 🎯 Usage Examples

Once configured, you can use the SonarQube MCP server in Windsurf:

### Code Analysis
```
Analyze this JavaScript code for issues:
function calculateTotal(items) {
  var total = 0;
  for (var i = 0; i < items.length; i++) {
    total += items[i].price;
  }
  return total;
}
```

### Project Management
```
- Search for issues in my projects
- Show me the quality gate status
- List all available programming languages
- Get system health information
```

### Available Tools

The MCP server provides these tools:

- **analyze_code_snippet** - Analyze code with SonarQube rules
- **search_sonar_issues_in_projects** - Search issues across projects
- **change_sonar_issue_status** - Update issue status (accept/false positive/reopen)
- **search_my_projects** - List your SonarQube projects
- **list_languages** - Show supported programming languages
- **get_component_measures** - Get quality metrics
- **search_metrics** - Search available metrics
- **list_quality_gates** - View quality gates
- **project_status** - Check project quality gate status
- **show_rule** - Display rule details
- **list_rule_repositories** - Show rule repositories
- **get_raw_source** - Get source code
- **get_scm_info** - Get SCM information
- **system_health** / **system_info** / **system_ping** - System status

## 🔧 Technical Details

### Prerequisites
- **Java 21** - Required for MCP server
- **Docker & Docker Compose** - For SonarQube
- **Windsurf** - For MCP integration

### Components

1. **SonarQube Setup**
   - Version: Community Edition 25.6.0
   - Database: PostgreSQL 15
   - Port: 9000
   - Token: `squ_REDACTED`

2. **MCP Server**
   - Source: [Official SonarSource MCP Server](https://github.com/SonarSource/sonarqube-mcp-server)
   - Version: 0.0.3-SNAPSHOT
   - Built locally with Java 21
   - Storage: `/tmp/mcp-sonarqube-storage`

### Directory Structure
```
├── docker-compose.yml          # SonarQube infrastructure
├── mcp-config.json            # Windsurf MCP configuration
├── sonarqube-mcp-server/      # Cloned MCP server source
├── .env                       # Environment variables
└── README.md                  # This file
```

## 🛠️ Troubleshooting

### SonarQube Not Starting
```bash
# Check container status
docker-compose ps

# View logs
docker-compose logs sonarqube
```

### MCP Server Issues
```bash
# Verify Java version
/opt/homebrew/opt/openjdk@21/bin/java --version

# Check storage directory
ls -la /tmp/mcp-sonarqube-storage/
```

### Connection Issues
```bash
# Test SonarQube API
curl -u "squ_fafc2f669e0523185b23b97a3663f4c7fed67dea:" http://localhost:9000/api/system/info
```

## 🏆 Success Factors

This setup works because:

1. **Local Build** - Avoids Docker storage permission issues
2. **Proper Java Path** - Uses full path to Java 21 binary
3. **Correct MCP Format** - Uses `mcpServers` wrapper for Windsurf
4. **Environment Variables** - Explicit JAVA_HOME and paths
5. **Stable Infrastructure** - Docker-based SonarQube with PostgreSQL

## 🔄 Maintenance

### Update MCP Server
```bash
cd sonarqube-mcp-server
git pull
export JAVA_HOME="/opt/homebrew/opt/openjdk@21"
export PATH="/opt/homebrew/opt/openjdk@21/bin:$PATH"
./gradlew clean build -x test
```

### Restart SonarQube
```bash
docker-compose restart
```

---

*This setup provides a complete, local code quality analysis environment with seamless Windsurf integration for enhanced development workflows.*
