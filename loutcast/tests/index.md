# Tests

## Purpose
Contains all classes/files related to tests.

## Files naming
- TestCase.php - Holds base setup for descendant tests.
- *Test.php - A test for the specific class.

## Structure

TestCase.php - Base TestCase for all Feature/Integration tests.
\
[Feature/](Feature) - Tests input/output of the application.
\
[Integration/](Integration) - Tests integration with third-party services.
\
[Unit/](Unit) - Tests exact classes apart of all dependencies.
\
[fixtures/](fixtures) - Holds constant pieces of data used for tests.
\
[Traits](Traits) - Miscelanious traits used in test classes. 

## Overview

### Основная цель написания тестов:
#### Проверка того что функционал работает ожидаемым образом.
#### Протоколирование того как должно работать приложение.
#### Возможность впоследствии проверить что внесенные изменения не нарушили существуюзщий функционал.

### Описание структуры обычного теста

```php
<?php

declare(strict_type=1);

namespace Tests\Unit\Services; // 

use App\Services\AccountsManager;
use PhpUnit\PhpUnit\MockObject;
use Tests\Unit\TestsCase;

/** @coversDefaultClass \App\Services\AccountsManager */
class AccountsManagerTest extends TestCase
{
	protected AccountsManager $manager;

	protected AccountsRepository&MockObject $accountsRepository;
	protected SsoClient&MockObject $ssoClient;

	public function setUp(): void
	{
		$this->accountsRepository = $this->createMock(AccountsRepository::class);
		$this->ssoClient = $this->createMock(SsoClient::class);

		$this->manager = new AccountsManager(500, $this->accountsRepository, $this->ssoClient);
	}

    /** @covers ::list */
	public function testList(): void
	{
		$accounts = [
			new Account(),
			new Account(),
			new Account(),
		];

		$this->accountsRepository->expects($this->once())
			->method('store')
			->willReturn($accounts);
		$this->accountsRepository->expects($this->once())
			->method('all')
			->willReturn($accounts);

		$this->ssoClient->

		$result = $this->manager->list();

		$this->assertSame($accounts, $result);
	}

	/** @covers ::list */
	public function testListForSsoApiNotAvailable()
	{
		$ssoClientException = new SsoClientException('Timeout error.');

		$this->ssoClient->expects($this->once())
			->method('index')
			->throwsException($ssoClientException)

		$this->expectsException(AccountManagerException::class);
		$this->expectsExceptionMessage('SSO Api is not available: Timeout exception.');

		$this->manager->list();
	}
}
```