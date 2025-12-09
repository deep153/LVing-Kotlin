# LVing - Code Property Graph Analyzer

A code analysis platform that generates and visualizes Code Property Graphs (CPG) for Rust programs. Upload Rust code, analyze it, and explore the generated graph through an interactive web interface.

## What You'll Need

### Required Software

1. **Docker Desktop**
   - **Download**: [Docker Desktop](https://www.docker.com/products/docker-desktop/) or you can also download from open source
   - **Version**: 20.10 or higher
   - **Platforms**: Windows, macOS, or Linux
   
2. **Docker Desktop Configuration**
   - **Memory (RAM)**: Allocate at least **4GB** of RAM to Docker
   - **How to configure**:
     - Open Docker Desktop
     - Go to Settings → Resources → Memory
     - Set memory to 4GB or more
     - Click "Apply & Restart"

3. **Disk Space**: At least 5GB free space

That's it! Docker will handle all other dependencies (Kotlin, Neo4j, Redis, etc.)

## Quick Setup (3 Steps)

### Step 1: Get the Code

```bash
# Clone the repository
git clone <repository-url>
cd lving-kotlin
```

### Step 2: Configure Environment

Create a file named `.env` in the project root directory. Copy the example file if provided and Update NEO4J_PASSWORD as per your choice

```bash
cp .env.example .env
# Then edit .env and set your password
```

### Step 3: Start the Application

```bash
# Start all services (this will take 2-3 minutes on first run)
docker-compose up --build

# The application is ready when you see "Application started"
```

> **First-time startup**: The first run downloads Docker images and builds the application, which takes 2-3 minutes. Subsequent starts are much faster (under 30 seconds).

## Access the Application

Once started, open your browser:

| Service | URL | Login |
|---------|-----|-------|
| **Web Application** | http://localhost:8080 | No login needed |
| **Neo4j Browser** (optional) | http://localhost:7474 | Username: `neo4j`<br>Password: (from your .env file) |

## Using the Application

1. **Open** http://localhost:8080 in your browser
2. **Upload** your Rust code or select a sample file
3. **Analyze** - Click the analyze button (takes 10-30 seconds)
4. **Query & Visualize** - Run queries to explore the code graph

## Environment Variables (.env file)

Your `.env` file should contain:

```bash
# REQUIRED: Set your Neo4j password
NEO4J_PASSWORD=your_secure_password_here

# Optional - only change if needed
# NEO4J_HOST=localhost
# NEO4J_USER=neo4j  
# REDIS_HOST=localhost
```

> **Security Note**: Never commit your `.env` file to git. It's already in `.gitignore`.

## Basic Example Queries

Once your project is analyzed, try these queries in the web interface:

```cypher
-- View all functions in your code
MATCH (n:FunctionDeclaration) RETURN n LIMIT 20

-- Find the main function
MATCH (n:MainFunctionDeclaration) RETURN n

-- See function calls
MATCH (caller)-[r:CALLS]->(callee)
RETURN caller, r, callee LIMIT 50
```

## Common Commands

### Starting and Stopping

```bash
# Start the application
docker-compose up

# Start in background (detached mode)
docker-compose up -d

# Stop the application
docker-compose down

# Stop and remove all data (fresh start)
docker-compose down -v
```

### Viewing Logs

```bash
# View all logs
docker-compose logs -f

# View logs from a specific service
docker-compose logs -f backend
```

### Checking Status

```bash
# Check if containers are running
docker-compose ps

# Should show: backend, neo4j, and redis as "Up"
```

## Troubleshooting Common Issues

### 1. Application Won't Start

**Problem**: Containers keep restarting or failing to start.

**Solution**:
```bash
# Check what's wrong
docker-compose logs

# Common fixes:
# 1. Check Docker memory is set to 4GB+ (Docker Desktop → Settings → Resources)
# 2. Make sure .env file exists with NEO4J_PASSWORD set
# 3. Try a fresh start:
docker-compose down -v
docker-compose up --build
```

### 2. Port Already in Use

**Problem**: Error says "port 8080 (or 7474) already in use".

**Solution**:
```bash
# Option 1: Stop whatever is using the port
# On Mac/Linux, find and kill the process:
lsof -i :8080
kill -9 <PID>

# Option 2: Use different ports
# Edit docker-compose.yml and change "8080:8080" to "8081:8080"
```

### 3. Application Takes Too Long or Crashes

**Problem**: Slow performance or Neo4j crashes.

**Solution**:
- Increase Docker Desktop memory allocation to 6GB or 8GB
- Docker Desktop → Settings → Resources → Memory → Apply & Restart

### 4. Can't Connect to Neo4j

**Problem**: "Authentication failed" or can't login to Neo4j Browser.

**Solution**:
```bash
# Reset everything with a fresh start
docker-compose down -v
echo "NEO4J_PASSWORD=newpassword" > .env
docker-compose up -d
```

### 5. Analysis Fails

**Problem**: Project status shows ERROR.

**Common reasons**:
- **Rust code has syntax errors**: Fix the code and try again
- **Code is too complex**: Start with a simpler Rust program
- **Neo4j not ready**: Wait 30 seconds after startup, then retry

### 6. Blank Web Page

**Problem**: Browser shows nothing at http://localhost:8080

**Solution**:
```bash
# Rebuild everything
docker-compose down
docker-compose up --build

# Wait for "Application started" message
```

### Getting Help

If you're still stuck:
1. Check `docker-compose logs` for error messages
2. Make sure Docker Desktop is running and has enough memory (4GB+)
3. Try `docker-compose down -v` and start fresh

## Sample Rust Files

The `dataset/` folder contains example Rust files you can use for testing:
- `buffer_overflow_high_rust_004.rs`
- `cell_race_high_rust_001.rs`
- `raw_ptr_race_high_rust_002.rs`
- And more...

You can upload these through the web interface to see how the analysis works.

## Technologies Used

- **Backend**: Kotlin with Ktor framework
- **Frontend**: React with TypeScript
- **Database**: Neo4j (graph database) + SQLite (metadata)
- **Cache**: Redis
- **Analysis**: Fraunhofer AISEC CPG library
- **Deployment**: Docker & Docker Compose

---

**Need Help?** Check the Troubleshooting section above or review the Docker logs with `docker-compose logs`
