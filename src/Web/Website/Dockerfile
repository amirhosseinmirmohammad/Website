# Use the SDK image for building
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build

# Set the working directory
WORKDIR /src

# Copy the project file and restore dependencies
COPY ["Website.csproj", "."]
RUN dotnet restore "./src/Website/Website.csproj"

# Copy the rest of the source code
COPY . .

# Change directory to where the csproj file is located
WORKDIR /src

# Build the project
RUN dotnet build "./src/Website/Website.csproj" -c Release -o /app/build

# Publish the application
FROM build AS publish
RUN dotnet publish "./src/Website/Website.csproj" -c Release -o /app/publish /p:UseAppHost=false

# Runtime image
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Website.dll"]
