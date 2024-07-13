# See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Step 1: Add debug command to list files before copying
RUN echo "Listing /src directory before COPY command" && ls -al /src

# Step 2: Copy the Website.csproj file
COPY ["Website.csproj", "/src/Web/Website/"]

# Step 3: Add debug command to list files after copying
RUN echo "Listing /src/Web/Website directory after COPY command" && ls -al /src/Web/Website

# Step 4: Restore dependencies
RUN dotnet restore "/src/Web/Website/Website.csproj"

# Step 5: Copy the remaining files
COPY . .

# Step 6: Change working directory
WORKDIR "/src/Web/Website"

# Step 7: Add debug command to list files before building
RUN echo "Listing /src/Web/Website directory before build" && ls -al /src/Web/Website

# Step 8: Build the project
RUN dotnet build "Website.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Step 9: Add debug command to list build output
RUN echo "Listing /app/build directory after build" && ls -al /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "Website.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Step 10: Add debug command to list publish output
RUN echo "Listing /app/publish directory after publish" && ls -al /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .

# Step 11: Add debug command to list final directory contents
RUN echo "Listing /app directory in final stage" && ls -al /app

ENTRYPOINT ["dotnet", "Website.dll"]
