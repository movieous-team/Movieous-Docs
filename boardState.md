# 状态管理

white-web-sdk 还提供多种工具，如选择器、铅笔、文字、圆形工具、矩形工具。同时还提供图片展示工具和 PPT 工具。这些功能的展现形式，关系到具体网页应用本身的交互设计、视觉风格。考虑到这一点，白板上没有直接提供这些 UI 组件。你只能通过程序调用的方式，来让白板使用这些功能。

对此，你可能有如下疑问：

* 我的 UI 组件已经做好了，我如何让 UI 组件的操作影响到白板的行为？
* 白板有哪些状态，我的 UI 组件如何监听、获取白板的状态？

本章将解决这 2 个问题。

# 白板状态

当你加入一个房间后，你可以获取和修改的房间状态有如下 3 个。

* __GlobalState__：全房间的状态，自房间创建其就存在。所有人可见，所有人可修改。
* __MemberState__：成员状态。每个房间成员都有独立的一份实例，成员加入房间时自动创建，成员离开房间时自动释放。成员只能获取、监听、修改自己的 MemberState。
* __BroadcastState__：视角状态，和主播模式、跟随模式相关。

这 3 个状态都是一个 key-value 对象。

你可以通过如下方式获取它们。

```javascript
var globalState = room.state.globalState;
var memberState = room.state.memberState;
var broadcastState = room.state.broadcastState;
```

其中 room 对象需要通过调用 `joinRoom` 方法获取，在之前的篇章中有说明，在此不再赘述。

这 2 个状态可能被动改变，比如 GlobalState 可能被房间其他成员修改。因此，你需要监听它们的变化。具体做法是，在调用 `joinRoom` 时带上回调函数。

```javascript
var callbacks = {
    onRoomStateChanged: function(modifyState) {
        if (modifyState.globalState) {
            // globalState 改变了
            var newGlobalState = modifyState.globalState;
        }
        if (modifyState.memberState) {
            // memberState 改变了
            var newMemberState = modifyState.memberState;
        }
        if (modifyState.broadcastState) {
            // broadcastState 改变了
            var broadcastState = modifyState.broadcastState;
        }
    },
};
room.joinRoom({uuid: uuid, roomToken: roomToken}, callbacks);
```

当你需要修改白板状态的时候，你可以使用如下方式修改。

```javascript
room.setGlobalState({...});
room.setMemberState({...});
```

你不需要在参数中传入修改后的完整 GlobalState 或 MemberState，只需要填入你希望更新的 key-value 对即可。如果你修改了 GlobalState，整个房间的人都会收到你的修改结果。

# GlobalState

```typescript
type GlobalState = {
    // 当前场景索引，修改它会切换场景
    currentSceneIndex: number;
};
```

# MemberState

```typescript
type MemberState = {

    // 当前工具，修改它会切换工具。有如下工具可供挑选：
    // 1. selector 选择工具
    // 2. pencil 铅笔工具
    // 3. rectangle 矩形工具
    // 4. ellipse 椭圆工具
    // 5. eraser 橡皮工具
    // 6. text 文字工具
    currentApplianceName: string;

    // 选择工具半径，越大选择工具越容易点选
    selectorRadius: number;

    // 线条的颜色，将 RGB 写在一个数组中。形如 [255, 128, 255]。
    strokeColor: [number, number, number];

    // 线条的粗细
    strokeWidth: number;

    // 文字的字号
    textSize: number;
};
```

# BroadcastState

```typescript
type BroadcastState = {

    // 当前视角模式，有如下：
    // 1."freedom" 自由视角，视角不会跟随任何人
    // 2."follower" 跟随视角，将跟随房间内的主播
    // 2."broadcaster" 主播视角，房间内其他人的视角会跟随我
    mode: ViewMode;

    // 房间主播 ID。
    // 如果当前房间没有主播，则为 undefined
    broadcasterId?: number;
};
```

