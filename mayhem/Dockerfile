FROM rustlang/rust:nightly AS builder

RUN cargo install cargo-fuzz && apt-get update && apt-get install -y wget unzip

ADD . /src
WORKDIR /src

RUN sed -i 's/Local\.timestamp(0, 0)/Local.timestamp_opt(0, 0).unwrap()/g'     src/options/update_rules.rs src/afterfact.rs

RUN mkdir -p .cargo &&     cargo vendor vendor/ 2>/dev/null > .cargo/config.toml

RUN python3 /src/fix_vendor.py

RUN wget https://github.com/omerbenamram/evtx/archive/refs/heads/master.zip &&     unzip master.zip && mkdir /testsuite && cp -r evtx-master/samples/* /testsuite/

RUN wget https://github.com/Yamato-Security/hayabusa-rules/archive/refs/heads/main.zip &&     unzip main.zip && mv hayabusa-rules-main /rules

RUN env RUSTFLAGS="-g -Cdebug-assertions=on" cargo +nightly build --release &&     mv target/release/hayabusa /hayabusa

FROM rustlang/rust:nightly
COPY --from=builder /hayabusa /
RUN mkdir /testsuite /rules /config
COPY --from=builder /src/config/* /config/
COPY --from=builder /testsuite/ /testsuite/
COPY --from=builder /rules/ /rules/
