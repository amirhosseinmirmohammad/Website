# Base image
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

# Build image
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["src/Web/Website/Website.csproj", "src/Web/Website/"]
RUN dotnet restore "src/Web/Website/Website.csproj"

COPY . .
WORKDIR "/src/src/Web/Website"
# Debugging line to check contents of /src/src/Web/Website
RUN echo "=== Start listing /src/src/Web/Website ===" && ls -al /src/src/Web/Website && echo "=== End listing /src/src/Web/Website ==="
RUN dotnet build "Website.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Publish image
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "Website.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Final image
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Website.dll"]
