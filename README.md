# sp1-debug-issues

A forkable repository to report SP1 issues.

## Steps

First, please check if your issue isn't one in the [common issues](https://docs.succinct.xyz/docs/developers/common-issues) page in our docs.

If not, please follow these steps:

* Fork this repo to your local machine
* Please prepending `SP1_DUMP=1` when running your normal program/script, it will output the `program.bin` and `stdin.bin` files to the current directory.
  Please note these 2 files are only generated when generating proofs (not in execution only mode).
* Then copy them to this repository
* Run the following commands in sequence:

## Go through these commands

SP1 can be used in several different modes: Mock, CPU, GPU and the Prover Network. To help debug the source of the issue and determine if it's specific to a prover mode, please follow the steps below (moving onto the next stage if the previous one succeeds). Let us know which stage your proof failed in.

* **Mock mode**  
  Run `cargo run --release -- mock`. If this fails, your error is within the execution of the program (and is hardware-agnostic).
* **Prover Network**  
  If you are generating a proof in CPU/GPU mode and are seeing failures, you should attempt generating a proof on the Prover Network, by running `cargo run --release -- network`. If [running your progam](https://docs.succinct.xyz/docs/generating-proofs/prover-network/usage) on the Prover Network works, the issue is likely due to an issue on your local setup. If it fails, this indicates something is wrong with the Prover Network when generating proofs.  
  
  > [!NOTE]
  > To test on the Prover Network, you need to have an API key and set it to the `NETWORK_PRIVATE_KEY` environment variable
* **GPU mode**  
  If you have a [supported GPU](https://docs.succinct.xyz/docs/generating-proofs/hardware-acceleration/cuda), Run `cargo run --release -- gpu` and see if it works.
* **CPU mode**  
  If testing on a GPU fails, Try on the CPU by running  `cargo run --release -- cpu` and see if it works.

If any of the above fail, report the command you ran and their output in the section below.

If all of the above succeed, the issue is likely due to the setup of your program not supplying valid input to the program (as you've now ran a stdin & program ELF that succeeds).

## Describe command for reproducible issue

Machine specs:

```
 Static hostname: testnet-prover
 Icon name: computer-vm
 Chassis: vm
 Machine ID: 5fa6be304a3c604a25565d74674f33b4
 Boot ID: f0cee139f2fa451f9d48e08cb2132b17
 Virtualization: kvm
 Operating System: Ubuntu 22.04.4 LTS
 Kernel: Linux 5.15.0-125-generic
 Architecture: x86-64
 Hardware Vendor: DigitalOcean
 Hardware Model: Droplet
```

**Please list the command here where you run into an issue**

### mock

* List the command: `RUST_LOG=info RUST_BACKTRACE=1 cargo run --release -- mock 2>&1 | tee mock-output.txt`
* Write the terminal output:

```
    Finished `release` profile [optimized] target(s) in 0.27s
     Running `target/release/sp1-debug-issues mock`
stderr: thread '<unnamed>' panicked at /home/ubuntu/.cargo/registry/src/index.crates.io-6f17d22bba15001f/hashbrown-0.14.5/src/raw/mod.rs:86:40:
stderr: Hash table capacity overflow
stderr: stack backtrace:
stderr: note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
thread 'main' panicked at src/main.rs:63:10:
failed to generate proof: execution failed with exit code 1

Stack backtrace:
   0: anyhow::error::<impl core::convert::From<E> for anyhow::Error>::from
   1: sp1_sdk::cpu::CpuProver::mock_prove_impl
   2: sp1_sdk::cpu::CpuProver::prove_impl
   3: <sp1_sdk::cpu::CpuProver as sp1_sdk::prover::Prover<sp1_prover::components::CpuProverComponents>>::prove
   4: <sp1_debug_issues::universal_prover::UniversalProver as sp1_sdk::prover::Prover<sp1_prover::components::CpuProverComponents>>::prove
   5: sp1_debug_issues::main
   6: std::sys::backtrace::__rust_begin_short_backtrace
   7: std::rt::lang_start::{{closure}}
   8: std::rt::lang_start_internal
   9: main
  10: <unknown>
  11: __libc_start_main
  12: _start
stack backtrace:
   0: rust_begin_unwind
   1: core::panicking::panic_fmt
   2: core::result::unwrap_failed
   3: sp1_debug_issues::main
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
```

### cuda

* List the command: `RUST_LOG=info RUST_BACKTRACE=1 cargo run --release -- cuda 2>&1 | tee cuda-output.txt`
* Write the terminal output:

```
    Finished `release` profile [optimized] target(s) in 0.32s
     Running `target/release/sp1-debug-issues cuda`
[2m2025-01-23T21:31:38.229171Z[0m [32m INFO[0m vk verification: true
[2m2025-01-23T21:31:43.169782Z[0m [32m INFO[0m [1msetup[0m: close [3mtime.busy[0m[2m=[0m916ms [3mtime.idle[0m[2m=[0m13.6µs
[2m2025-01-23T21:31:44.665693Z[0m [32m INFO[0m [1mdeserializing proof request[0m: close [3mtime.busy[0m[2m=[0m73.9ms [3mtime.idle[0m[2m=[0m7.25µs
[2m2025-01-23T21:31:44.707752Z[0m [32m INFO[0m [1mprove core[0m: cpu_memory_gb=236, gpu_memory_gb=84
[2m2025-01-23T21:31:44.778542Z[0m [32m INFO[0m [1mprove core[0m:[1mprove_core[0m: clk = 0 pc = 0x335c74    
[2m2025-01-23T21:31:44.779163Z[0m [32m INFO[0m [1mprove core[0m:[1mprove_core[0m: ┌╴boot-load    
[2m2025-01-23T21:31:44.785821Z[0m [32m INFO[0m [1mprove core[0m:[1mprove_core[0m: └╴167,500 cycles    
[2m2025-01-23T21:31:44.785847Z[0m [32m INFO[0m [1mprove core[0m:[1mprove_core[0m: ┌╴oracle-load    
[2m2025-01-23T21:31:44.943245Z[0m [32m INFO[0m [1mprove core[0m:[1mprove_core[0m: │ ┌╴in-memory-oracle-from-raw-bytes-archive    
[2m2025-01-23T21:31:44.943290Z[0m [32m INFO[0m [1mprove core[0m:[1mprove_core[0m: │ └╴388 cycles    
[2m2025-01-23T21:31:44.943310Z[0m [32m INFO[0m [1mprove core[0m:[1mprove_core[0m: │ ┌╴in-memory-oracle-from-raw-bytes-deserialize    
stderr: thread '<unnamed>' panicked at /home/ubuntu/.cargo/registry/src/index.crates.io-6f17d22bba15001f/hashbrown-0.14.5/src/raw/mod.rs:86:40:
stderr: Hash table capacity overflow
stderr: stack backtrace:
stderr: note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
thread '<unnamed>' panicked at /root/.cargo/git/checkouts/sp1-wip-7e8893292eb2977e/5bee823/crates/core/machine/src/utils/prove.rs:427:80:
called `Result::unwrap()` on an `Err` value: ExecutionError(HaltWithNonZeroExitCode(1))
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
thread 'tokio-runtime-worker' panicked at /root/.cargo/git/checkouts/sp1-wip-7e8893292eb2977e/5bee823/crates/prover/src/lib.rs:372:64:
called `Result::unwrap()` on an `Err` value: Any { .. }
[2m2025-01-23T21:31:44.944404Z[0m [32m INFO[0m [1mprove core[0m:[1mprove_core[0m: close [3mtime.busy[0m[2m=[0m118ms [3mtime.idle[0m[2m=[0m119ms
[2m2025-01-23T21:31:44.944432Z[0m [32m INFO[0m [1mprove core[0m: close [3mtime.busy[0m[2m=[0m274ms [3mtime.idle[0m[2m=[0m5.54µs
panic: called `Result::unwrap()` on an `Err` value: Any { .. }
thread 'main' panicked at /root/.cargo/registry/src/index.crates.io-6f17d22bba15001f/sp1-cuda-4.0.1/src/lib.rs:265:82:
called `Result::unwrap()` on an `Err` value: HttpError { status: 500, msg: "unknown error", path: "/twirp/api.ProverService/ProveCore", content_type: "called `Result::unwrap()` on an `Err` value: Any { .. }" }
stack backtrace:
   0: rust_begin_unwind
   1: core::panicking::panic_fmt
   2: core::result::unwrap_failed
   3: sp1_cuda::SP1CudaProver::prove_core
   4: <sp1_sdk::cuda::CudaProver as sp1_sdk::prover::Prover<sp1_prover::components::CpuProverComponents>>::prove
   5: <sp1_debug_issues::universal_prover::UniversalProver as sp1_sdk::prover::Prover<sp1_prover::components::CpuProverComponents>>::prove
   6: sp1_debug_issues::main
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
```

### cpu

* List the command: `RUST_LOG=info RUST_BACKTRACE=1 cargo run --release -- cpu 2>&1 | tee cpu-output.txt`
* Write the terminal output:

```
    Finished `release` profile [optimized] target(s) in 0.25s
     Running `target/release/sp1-debug-issues cpu`
stderr: thread '<unnamed>' panicked at /home/ubuntu/.cargo/registry/src/index.crates.io-6f17d22bba15001f/hashbrown-0.14.5/src/raw/mod.rs:86:40:
stderr: Hash table capacity overflow
stderr: stack backtrace:
stderr: note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
thread '<unnamed>' panicked at /root/.cargo/registry/src/index.crates.io-6f17d22bba15001f/sp1-core-machine-4.0.1/src/utils/prove.rs:427:80:
called `Result::unwrap()` on an `Err` value: ExecutionError(HaltWithNonZeroExitCode(1))
stack backtrace:
   0: rust_begin_unwind
   1: core::panicking::panic_fmt
   2: core::result::unwrap_failed
   3: sp1_core_machine::utils::prove::prove_core_stream::{{closure}}
   4: std::thread::scoped::scope
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
thread 'main' panicked at /root/.cargo/registry/src/index.crates.io-6f17d22bba15001f/sp1-prover-4.0.1/src/lib.rs:372:64:
called `Result::unwrap()` on an `Err` value: Any { .. }
stack backtrace:
   0: rust_begin_unwind
   1: core::panicking::panic_fmt
   2: core::result::unwrap_failed
   3: std::thread::scoped::scope
   4: sp1_prover::SP1Prover<C>::prove_core
   5: sp1_sdk::cpu::CpuProver::prove_impl
   6: <sp1_sdk::cpu::CpuProver as sp1_sdk::prover::Prover<sp1_prover::components::CpuProverComponents>>::prove
   7: <sp1_debug_issues::universal_prover::UniversalProver as sp1_sdk::prover::Prover<sp1_prover::components::CpuProverComponents>>::prove
   8: sp1_debug_issues::main
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
```
