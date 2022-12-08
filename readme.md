## 6. 트러블 슈팅

### 1. 하위 컴포넌트의 요소를 가져오지 못 하는 이슈

-   문제 상황
    -   상위 컴포넌트에서 하위 컴포넌트의 요소를 선택해서 클릭 이벤트 또는 textContent를 가져와야 하는 상황에서 그 요소만 가져올 수 없다.
-   원인 추론
    -   클래스로 컴포넌트화하여 해당 컴포넌트를 render(or initialize) 하여야 요소들이 만들어지고 appendChild 하여야 해당 index.html에 그려져 querySelector로 가져올 수 없다.
-   해결 방법
    -   render 할 때 배열 형태로 보내주고 싶은 요소를 같이 보내준 후 해당 컴포넌트를 부를 때 구조 분해 할당으로 가져와 준다.
-   적용 코드)

```js
// ./src/components/PostUploadForm/postUploadPreview.js
import Component from '../../core/Component.js';

class PostUploadPreview extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        const previewImgCon = document.createElement('div');
        previewImgCon.setAttribute('class', 'post_img_con');

        const previewImg = document.createElement('img');
        previewImg.setAttribute('src', this.props);
        previewImg.setAttribute('class', 'post_img_preview');

        const imgCancelBtn = document.createElement('button');
        imgCancelBtn.setAttribute('class', 'post_btn_img_cancel');
        imgCancelBtn.textContent = 'x';

        previewImgCon.appendChild(previewImg);
        previewImgCon.appendChild(imgCancelBtn);

        return [previewImgCon, imgCancelBtn]; // 배열의 형태로 보내준다.
    }
}
export default PostUploadPreview;
```

```js
// ./src/components/PostUploadForm/postUploadForm.js
const [postUploadPreviewEl, imgCancelBtn] = postUploadPreview.initialize();
```
