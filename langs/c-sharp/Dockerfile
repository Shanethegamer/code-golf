FROM mcr.microsoft.com/dotnet/sdk:6.0.101-alpine3.14 as builder

# /dev/urandom is needed.
RUN mkdir /empty /mydev \
 && mknod -m 444 /mydev/urandom c 1 9

ENV DOTNET_CLI_TELEMETRY_OPTOUT=1 VERSION=4.0.1

WORKDIR /source

COPY Compiler.csproj Compiler.cs ./

RUN dotnet add package Microsoft.CodeAnalysis.CSharp -v $VERSION \
 && dotnet publish -c Release -r linux-musl-x64 --self-contained -o /compiler

FROM scratch

COPY --from=0 /mydev                   /dev
COPY --from=0 /lib/ld-musl-x86_64.so.1 \
              /lib/libcrypto.so.1.1    \
              /lib/libssl.so.1.1       /lib/
COPY --from=0 /empty                   /proc
COPY --from=0 /empty                   /tmp
COPY --from=0 /usr/lib                 /usr/lib/
COPY --from=0 /compiler                /compiler/

ENTRYPOINT ["/compiler/Compiler"]

CMD ["--version"]
