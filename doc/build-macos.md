# Mac macOS Build Instructions and Notes
The commands in this guide should be executed in a Terminal application.
The built-in one is located in `/Applications/Utilities/Terminal.app`.

## Preparation
Install the macOS command line tools:

```bash
xcode-select --install
```

When the popup appears, click `Install`.

Then install [Homebrew](http://brew.sh).

## Dependencies

```bash
brew install automake berkeley-db4 libtool boost --c++11 miniupnpc openssl pkg-config protobuf --c++11 qt5 libevent
```

See [dependencies.md](dependencies.md) for a complete overview.

If you want to build the disk image with `make deploy` (.dmg / optional), you need RSVG

```bash
brew install librsvg
```

NOTE: Must build with QT 5.3 or higher. Building with Qt4 is not supported.

## Build Bitcoin Unlimited

1. Clone the Bitcoin source code and cd into `BitcoinUnlimited`
    ```bash
    git clone https://github.com/BitcoinUnlimited/BitcoinUnlimited.git
    cd BitcoinUnlimited
    ```

2.  Build:

    Configure and build the headless Bitcoin binaries as well as the GUI (if Qt is found).

    You can disable the GUI build by passing `--without-gui` to configure.

    ```bash
    ./autogen.sh
    ./configure
    make
    ```

3.  It is recommended to build and run the unit tests:

    ```bash
    make check
    ```

4.  You can also create a .dmg that contains the .app bundle (optional):

    ```bash
    make deploy
    ```

## Running

Bitcoin Unlimited is now available at `./src/bitcoind`

Before running, it's recommended you create an RPC configuration file.

```bash
echo -e "rpcuser=bitcoinrpc\nrpcpassword=$(xxd -l 16 -p /dev/urandom)" > "/Users/${USER}/Library/Application Support/Bitcoin/bitcoin.conf"

chmod 600 "/Users/${USER}/Library/Application Support/Bitcoin/bitcoin.conf"
```

The first time you run bitcoind, it will start downloading the blockchain. This process could take several hours.

You can monitor the download process by looking at the debug.log file:

```bash
tail -f $HOME/Library/Application\ Support/Bitcoin/debug.log
```

## Other commands:

```bash
./src/bitcoind -daemon # Starts the Bitcoin daemon.
./src/bitcoin-cli --help # Outputs a list of command-line options.
./src/bitcoin-cli help # Outputs a list of RPC commands when the daemon is running.
```

## Using Qt Creator as IDE

You can use Qt Creator as an IDE, for Bitcoin development.
Download and install the community edition of [Qt Creator](https://www.qt.io/download/).
Uncheck everything except Qt Creator during the installation process.

1. Make sure you installed everything through Homebrew mentioned above
2. Do a proper `./configure --enable-debug`
3. In Qt Creator do "New Project" -> Import Project -> Import Existing Project
4. Enter "bitcoin-qt" as project name, enter src/qt as location
5. Leave the file selection as it is
6. Confirm the "summary page"
7. In the "Projects" tab select "Manage Kits..."
8. Select the default "Desktop" kit and select "Clang (x86 64bit in /usr/bin)" as compiler
9. Select LLDB as debugger (you might need to set the path to your installation)
10. Start debugging with Qt Creator

## Notes

* Tested on OS X 10.8 through macOS 10.12 on 64-bit Intel processors only.

* Building with downloaded Qt binaries is not officially supported. See the notes in [#7714](https://github.com/bitcoin/bitcoin/issues/7714)
