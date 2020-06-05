Migration guides
================

Migration from Waffle 2.*.* to Waffle 2.3.0 and Waffle 2.4.0
------------------------------------------------------------

**Created monorepo**

Packages :code:`waffle-cli`, :code:`waffle-chai`, :code:`waffle-provider`, :code:`waffle-compiler` were migrated to monorepo.

**Created MockProvider class**

We added MockProvider class, it was change the way to create the provider.

.. tabs::

  .. group-tab:: Waffle 2.*.*

    .. code-block:: ts

      await createMockProvider(options);

  .. group-tab:: Waffle 2.3.0

    .. code-block:: ts

      provider = new MockProvider();


:code:`new MockProvider` returned :code:`providers.Web3Provider`, which is the parent class of :code:`MockProvider`.

**Reorganise getWallets() method**

.. tabs::

  .. group-tab:: Waffle 2.*.*

    .. code-block:: ts

      await getWallets(provider);

  .. group-tab:: Waffle 2.3.0

    .. code-block:: ts

      wallets = new MockProvider().getWallets()

**Renamed configuration options**

We renamed configuration options to compile contracts.

.. tabs::

  .. group-tab:: Waffle 2.*.*

    .. code-block:: text

      {
        sourcesPath: "...",
        targetPath: "...",
        npmPath: "...",
        compiler: "...",
        docker-tag: "...",
        solcVersion: "...",
        legacyOutput: "...",
        allowedPaths: "...",
        ganacheOptions: "..."
      }


  .. group-tab:: Waffle 2.4.0

    .. code-block:: text

      {
        sourceDirectory: "...",
        outputDirectory: "...",
        nodeModulesDirectory: "...",
        compilerType: "...",
        compilerVersion: "...",
        compilerAllowedPaths: "...",
        compilerAllowedPaths: "..."
      }

Migration from Waffle v2 to Waffle v3
-------------------------------------

There are some new functionality and some slight refactoring and improved paradigms in Waffle v3.

**Removed deprecated APIs from provider**

In :code:`Waffle 3.0.0` we remove deprecated APIs from provider.

**Swapped arguments for Fixture**

In :code:`Waffle 3.0.0` we swapped arguments for Fixture, because the provider argument is very rarely used compared to wallets.
So such implantation should be more convenient for users.

.. tabs::

  .. group-tab:: Waffle 2.5.0

    .. code-block:: ts

      type Fixture<T> = (provider: MockProvider, wallets: Wallet[]) => Promise<T>;

      export function createFixtureLoader(overrideProvider?: MockProvider, overrideWallets?: Wallet[]) {
        ...
      }

  .. group-tab:: Waffle 3.0.0

    .. code-block:: ts

      type Fixture<T> = (wallets: Wallet[], provider: MockProvider) => Promise<T>;

      export function createFixtureLoader(overrideWallets?: Wallet[], overrideProvider?: MockProvider) {
        ...
      }

**Added automatic recognising waffle.json config without cli argument**

Waffle recognises :code:`waffle.json` as default configuration file. If your configuration file is called
:code:`waffle.json`, it's possible to use just :code:`waffle` to build contracts.

.. tabs::

  .. group-tab:: Waffle 2.5.0

    .. code-block:: json

      {
        "scripts": {
          "build": "waffle waffle.json"
        }
      }



  .. group-tab:: Waffle 3.0.0

    .. code-block:: json

      {
        "scripts": {
          "build": "waffle"
        }
      }

**Introduced MockProviderOptions**

We added MockProviderOptions. It will be convenient in the future, when the provider may need some
other options than :code:`ganacheOptions`.

.. tabs::

  .. group-tab:: Waffle 2.5.0

    .. code-block:: ts

      constructor(private options?: MockProviderOptions) {
        super(Ganache.provider({accounts: defaultAccounts, ...options}) as any);
        this._callHistory = new CallHistory();
        this._callHistory.record(this);
      }


  .. group-tab:: Waffle 3.0.0

    .. code-block:: ts

      interface MockProviderOptions {
        ganacheOptions: Ganache.IProviderOptions;
      }

      constructor(private options?: MockProviderOptions) {
        super(Ganache.provider({accounts: defaultAccounts, ...options?.ganacheOptions}) as any);
        this._callHistory = new CallHistory();
        this._callHistory.record(this);
      }

**Dropped support for contract.interface**

We dropped support for contract.interface, because it duplicated contract ABI. Now we support just :code:`contract.abi`.

.. tabs::

  .. group-tab:: Waffle 2.5.0

    .. code-block:: text

      {
        "abi": [
          ...
        ],
        "interface: [
          ...
        ],
        "evm": {
          ...
        },
        "bytecode": "..."
      }

  .. group-tab:: Waffle 3.0.0

    .. code-block:: text

      {
        "abi": [
          {...}
        ],
        "evm": {
          ...
        },
        "bytecode": "..."
      }
