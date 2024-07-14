#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

# Depending on the target system you want to run the container on
# You might have to remove the 'platform' argument to fix errors like
# Error: no match for platform in manifest

# Use ASP.NET core as base image
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

# Use .NET SDK as build image
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY ["src/Web/Website/Website.csproj", "src/Web/Website/"]
RUN dotnet restore "src/Web/Website/Website.csproj"
COPY . .
WORKDIR "/src/Web/Website"
RUN dotnet build "Website.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "Website.csproj" -c Release -o /app/publish /p:UseAppHost=false

# Use base image to create the final container
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Website.dll"]
