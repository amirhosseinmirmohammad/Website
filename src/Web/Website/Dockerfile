# Use the ASP.NET runtime image as the base image
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

# Use the .NET SDK image for building the application
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Step 1: Copy the .csproj file to the container
COPY ["Website.csproj", "./Web/Website/"]

# Debug: List contents of /src directory
RUN echo "Contents of /src before dotnet restore" && ls -al /src

# Step 2: Restore the dependencies
RUN dotnet restore "./Web/Website/Website.csproj"

# Debug: List contents of /src/Web/Website directory after restore
RUN echo "Contents of /src/Web/Website after dotnet restore" && ls -al /src/Web/Website

# Step 3: Copy the rest of the application files
COPY . .

# Debug: List contents of /src directory after copy
RUN echo "Contents of /src after copying all files" && ls -al /src

# Step 4: Change working directory to the project folder
WORKDIR "/src/Web/Website"

# Debug: Print the current working directory
RUN echo "Current working directory" && pwd

# Debug: List contents of the working directory
RUN echo "Contents of /src/Web/Website before dotnet build" && ls -al /src/Web/Website

# Step 5: Build the project
RUN dotnet build "./Website.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Debug: List contents of /app/build directory after build
RUN echo "Contents of /app/build after dotnet build" && ls -al /app/build

# Use the build stage for publishing
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./Website.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Use the base runtime image for the final stage
FROM base AS final
WORKDIR /app

# Copy the published files from the build stage
COPY --from=publish /app/publish .

# Set the entry point for the container
ENTRYPOINT ["dotnet", "Website.dll"]
