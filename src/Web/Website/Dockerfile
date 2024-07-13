# See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Copy the csproj file
COPY ["src/Web/Website/Website.csproj", "src/Web/Website/"]
RUN echo "Contents of /src directory after copying csproj:" && ls -al /src
RUN echo "Contents of /src/Web/Website directory after copying csproj:" && ls -al /src/Web/Website

# Restore dependencies
RUN dotnet restore "src/Web/Website/Website.csproj"

# Copy the rest of the files
COPY . .
RUN echo "Contents of /src directory after copying all files:" && ls -al /src
RUN echo "Contents of /src/src/Web/Website directory before build:" && ls -al /src/src/Web/Website

# Set the working directory
WORKDIR "/src/src/Web/Website"

# Build the project
RUN dotnet build "Website.csproj" -c $BUILD_CONFIGURATION -o /app/build || { echo 'dotnet build failed'; exit 1; }

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "Website.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Website.dll"]
