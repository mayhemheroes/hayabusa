# Build Stage
FROM ghcr.io/evanrichter/cargo-fuzz:latest as builder

## evtx samples
RUN wget https://github.com/omerbenamram/evtx/archive/refs/heads/master.zip && \
    unzip master.zip && \
    mkdir /testsuite && \
    cp evtx-master/samples/* /testsuite/

## get default rules
RUN wget https://github.com/Yamato-Security/hayabusa-rules/archive/refs/heads/main.zip && \
    unzip main.zip && \
    mv hayabusa-rules-main /rules

## Add source code to the build stage.
ADD . /src
WORKDIR /src

RUN env RUSTFLAGS="-g -Cdebug-assertions=on" cargo +nightly -Z sparse-registry build --release && \
    mv target/release/hayabusa /hayabusa

# Package Stage
FROM rustlang/rust:nightly

COPY --from=builder /hayabusa /
RUN mkdir /testsuite /rules /config
COPY --from=builder /src/config/* /config/
COPY --from=builder /testsuite/ /testsuite/
COPY --from=builder /rules/ /rules/
