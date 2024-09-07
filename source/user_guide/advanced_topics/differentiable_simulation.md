# 🪐 Differentiable Simulation

## genesis.Tensor
We now have our own tensor data type: `genesis.Tensor()`, for the following reasons:
- to ensure a consistent user experience :)
- it enables end-to-end gradient flow from loss all the way back to action input
- it removes need for specifying datatype (though you still can) when creaing tensors. The datatype specified when calling gs.init() will be used when creating genesis tensors.
- provides additional safety checks, such as contiguous check and check if tensors from different Scene are accidently being merged into the same computation graph.
- supports other potential customizations if we need.

This is essentially a subclass of pytorch tensors, so users can simply treat it as torch tensors and apply different kinds of torch operations.

In pytorch, the recommended way of creating tensors is to call `torch.tensor` and other tensor creation ops like `torch.rand`, `torch.zeros`, `torch.from_numpy`, etc. We aim to reproduce the same experience in genesis, and genesis tensors can be created simply by replacing `torch` with `genesis`, e.g.
```
x = gs.tensor([0.5, 0.73, 0.5])
y = gs.rand(size=(horizon, 3), requires_grad=True)
```
Tensors created this way are leaf tensors, and their gradient can be accessed with `tensor.grad` after backward pass. Pytorch operations mixing torch and genesis tensors will automatically yield genesis tensors.

There exist a few minor differences though:
- Similar to torch, genesis tensor creation automatically infers the data type of the tensor based on the parameter (whether int or float), but then will convert to the float or int type with precision specified in gs.init().
- Users can also can override datatypes, and now we support `dtype=int` or `dtype=float` when calling the tensor creation ops.
- All genesis tensors are on cuda, so device selection is not allowed. This means unlike `torch.from_numpy`, `genesis.from_numpy` directly gives you tensors on cuda.
- `genesis.from_torch(detach=True)`: this creates a genesis tensor given a torch tensor. When calling this, if detach is True, the returned genesis tensor will be a new leaf node, detached from pytorch's computation graph. If detach is False, the returned genesis tensor will be connected to the upstream torch computation graph, and when calling backward pass, the gradient will flow back all the way to connected pytorch tensors. By default, we should use purely genesis tensors, but this allows potential integration with upstream applications built on pytorch, e.g. training neural policies.
- Each genesis tensor object has a `scene` attribute. Any child tensors derived from it would inherit the same scene. This lets us keep track of the source of the gradient flow.
    ```python
    state = scene.get_state()
    # state.pos is a genesis tensor
    print(state.pos.scene) # output: <class 'genesis.engine.scene.Scene'> id: 'e1a95be2-0947-4dcb-ad02-47b8541df0a0'
    random_tensor = gs.rand(size=(), requires_grad=True)
    print(random_tensor.scene) # output: None
    pos_ = state.pos + random_tensor
    print(pos_.scene) # output: <class 'genesis.engine.scene.Scene'> id: 'e1a95be2-0947-4dcb-ad02-47b8541df0a0'
    ```
    If `tensor.scene` is `None`, `tensor.backward()` behaves identically to a torch tensor's `backward()`. Otherwise, it will allow gradient flow back to `tensor.scene` and trigger upstream gradient flow.
- To resemble torch's behavior like `nn.Module.zero_grad()` or `optimizer.zero_grad()`, you can also do `tensor.zero_grad()` with a genesis tensor.

**NB**: This is just a initial implementation and subject to future changes if needed. Suggestions are welcome.

