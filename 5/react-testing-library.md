# React Testing Library

구현 기반의 테스트 도구인 Enzyme의 대안으로 자리 잡은 테스트 도구이다. RTL은 세부적인 구현사항보다는 실제 사용자 경험과 유사한 방식의 테스트를 작성할 것을 권고한다.

```html
<div>Hello World</div>
```

RTL은 div 태그를 사용하는지 보다 Hello World 메시지가 브라우저에 노출이 되는지 파악하는 것을 더 중요하다고 본다. 이와 같이 구현보다는 기능에 초점을 맞춘 테스트 방식은 신뢰도를 높임과 동시에 코드 리팩토링 시 테스트 코드 수정 빈도를 줄일 수 있다.

RTL이 Jest를 포함하는 구조이다. 전반적으로 Jest를 통해 기능 테스트를 진행할 수는 있지만, React의 컴포넌트를 렌더링하고 테스트하기 위해서는 몇가지 기능이 더 필요하다.

* Jest - 자체적인 test runner와 test util 제공
* RTL - Jest + React 컴포넌트 test util 제공

#### 예제

***

```jsx
import { render, screen } from '@testing-library/react';

import TextField from './TextField';

test('TextField', () => {	
	const text = 'Tester';
	const setText = () => {
		// do nothing...
	};

	render((	
		<TextField
			label="Name"
			placeholder="Input your name"
			text={text}
			setText={setText}		
		/>
	));
	
	screen.getByLabelText('Name');
});
```

테스트 코드를 작성하면 인터페이스를 점검할 수 있다. 위 컴포넌트를 예로 들면 (1) label이 빠져있고 (2) text 같이 범용적인 표현을 사용하지 않았던 문제가 있었다.

개발하면서 이런 문제를 발견할 수도 있지만, 테스트부터 작성했거나 빠르게 테스트 코드를 작성했다면 작성하기 전 또는 바로 직후에 문제를 발견해서 수정할 수 있었을 것이다.

시간이 지나면 해당 코드에 대한 지식이 감소하고, 자신감 또한 감소하기 때문에 건드리기 힘든 코드가 되기 쉽상이다.

#### BDD 스타일

***

```jsx
import { render, screen, fireEvent } from '@testing-library/react';

import TextField from './TextField';

const context = describe;

describe('TextField', () => {
	const text = 'Tester';
	const setText = jest.fn();
	
	beforeEach(() => {
		setText.mockClear();
		// 또는 jest.clearAllMocks();	
	});
	
	function renderTextField() {
		render((
			<TextField
				label="Name"
				placeholder="Input your name"
				text={text}
				setText={setText}
			/>
		));
	}
	
	it('renders an input control', () => {
		renderTextField();

		screen.getByLabelText('Name');
	});
	
	context('when user types text', () => {	
		it('calls the change handler', () => {
			renderTextField();

			fireEvent.change(screen.getByLabelText('Name'), {
				target: {
					value: 'New Name',
				},
			});
	
			expect(setText).toBeCalledWith('New Name');
		});
	});
});
```

반복되는 코드를 Extract Function 리팩터링 하고, fireEvent 등을 통해 인터랙션만 검증한다. 만약 복잡한 로직이 컴포넌트로부터 분리된다면, 여기서는 이것만 검증하면 된다.

만약 외부 의존성이 큰 코드를 작성한다면, 해당 부분만 가짜로 구현할 수 있다.

#### Mocking

***

```jsx
import { render, screen } from '@testing-library/react';

import App from './App';

**jest.mock**('./hooks/useFetchProducts', () => () => [
	{
		category: 'Fruits', price: '$1', stocked: true, name: 'Apple',
	},
]);

test('App', () => {
	render(<App />);

	screen.getByText('Apple');
});
```

`useFetchProducts`는 서버와 의존한다. 테스트를 진행할 때 마다 서버를 가동하긴 힘든 일이기에 약속된 인터페이스가 맞는지 임의로 확인한다.

일반적으론 백엔드와 소통하는 부분이 차지하는 비중이 큰데, 이 부분을 하나씩 가짜 구현으로 바꾸다 보면 어려울때가 있다.
