﻿# STAGE 1 - COMPILE AND PUBLISH

# use the image that has the build tools for the "build stage"
FROM microsoft/dotnet:2.1-sdk-alpine AS build-env

# set the working directory in the image as "app"
WORKDIR /app

# caches results of nuget restore by copying csproj file separately
#COPY ./HttpNatsProxy.sln HttpNatsProxy.sln
COPY ./Proxy/Proxy.csproj ./Proxy/Proxy.csproj
COPY ./Proxy.Shared/Proxy.Shared.csproj ./Proxy.Shared/Proxy.Shared.csproj
RUN dotnet restore ./Proxy/Proxy.csproj
# RUN dotnet restore ./Proxy.Shared/Proxy.Shared.csproj

# copy the rest of the code
COPY . ./

# publish
RUN dotnet publish ./Proxy/Proxy.csproj -c Release -o /publish

# STAGE 2 - BUILD RUNTIME OPTIMIZED IMAGE

# use the runtime optimized image that does not have any build tools, only the .net core runtime
FROM microsoft/dotnet:2.1-runtime-alpine

# set the working directory in the image as "app"
WORKDIR /app

# copy the compiled code from the published output folder of the "build stage" into this image
COPY --from=build-env /publish .

# set the default command that will run when this image is ran
ENTRYPOINT [ "dotnet", "Proxy.dll" ]