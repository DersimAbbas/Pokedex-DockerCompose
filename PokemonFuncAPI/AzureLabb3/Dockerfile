# See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

# This stage is used when running from VS in fast mode (Default for Debug configuration)
FROM mcr.microsoft.com/azure-functions/dotnet-isolated:4-dotnet-isolated8.0 AS base
WORKDIR /home/site/wwwroot

# This stage is used to build the service project
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["AzureLabb3.csproj", "./"]
RUN dotnet restore "./AzureLabb3.csproj"
COPY . .
WORKDIR "/src"
RUN dotnet build "AzureLabb3.csproj" -c $BUILD_CONFIGURATION -o /app/build

# This stage is used to publish the service project to be copied to the final stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./AzureLabb3.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# This stage is used in production or when running from VS in regular mode (Default when not using the Debug configuration)
FROM base AS final
WORKDIR /home/site/wwwroot
COPY --from=publish /app/publish .
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
	AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
	ConnectionStrings__cosmosdb="AccountEndpoint=https://<your-cosmosdb-account>.documents.azure.com:443/;AccountKey=<your-cosmosdb-key>;" \
	ASPNETCORE_URLS=http://+:8081 \
	CORS_ALLOWED_ORIGINS="[\"*\"]" \
	CONTAINER_NAME="frontend.dev" 