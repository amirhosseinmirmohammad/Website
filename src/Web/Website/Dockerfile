# Stage 1: Base image for building
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# Copy the project file and restore dependencies
COPY ["src/Web/Website/Website.csproj", "src/Web/Website/"]
RUN dotnet restore "src/Web/Website/Website.csproj"

# Copy the entire source code
COPY . .

# Build the project
WORKDIR "/src/src/Web/Website"
RUN dotnet build "Website.csproj" -c Release -o /app/build

# Stage 2: Publish the application
FROM build AS publish
RUN dotnet publish "Website.csproj" -c Release -o /app/publish /p:UseAppHost=false

# Stage 3: Runtime image
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Website.dll"]
