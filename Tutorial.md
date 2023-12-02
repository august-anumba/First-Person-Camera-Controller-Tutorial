# First Person Camera Controller Tutorial

This tutorial shows how to create a first person camera controller in Unity.

## 1. Creating and set up both parent and child `Player` GameObjects.

In your chosen scene, create a new empty GameObject by right clicking `Player`.

To this game object, add a rigidbody and set the `Collision Detection` to `Continuous`, set `Interpolate` to `Interpolate`,  and freeze the `X`, `Y` and `Z` rotation under `Constraints`.

Under the `Player` game object, create a 3D capsule to act as the player object and name this to `PlayerObject`.

Under the `Player` game object, create a empty game object to act as the player orientation and name this to `Orientation`.

Under the `Player` game object, create a empty game object to act as the camera position and name this to `CameraPosition` and move this to where you would like the camera to be positioned on the player.

Now create a new empty game object seperate from the `Player` and name this `CameraHolder`, drag the `Main Camera` under this new game object and reset the transform via the inspector.

## 2. Create scripts to control the camera.

### Script to move camera position with the position of the player.

In the `Project` window of the editor, create a folder and call it `Scripts`.

Under this new folder create a new script and call it `MoveCamera`.

In this script we are making sure that the `CameraHolder` is set to the `CameraPosition` game object under the `Player`:
```.cs
public class MoveCamera : MonoBehaviour
{
    public Transform cameraPosition;
    private void Update()
    {
        transform.position = cameraPosition.position;
    }
}
```

Now go back into Unity and drag the `MoveCamera` script under the `CameraHolder` game object, now under `Camera Position` in the script, drag the `CameraPosition` game object from under the `Player` into this field.

### Script to control the movement of the camera.

In the scripts folder create a new script and call it `PlayerCamera`.

This script contols the movement of the `Main Camera` with your mouse:
```.cs
public class PlayerCamera : MonoBehaviour
{
    [Header("Camera Properties")]
    public float xSensitivity = 400f;
    public float ySensitivity = 400f;
    [Range(40f, 90f)]public float cameraClampAngle = 80f;

    public Transform cameraOrientation;

    private float _xRotation;
    private float _yRotation;
    private float _mouseX;
    private float _mouseY;

    private void Start()
    {
        //Hide cursor and lock it to the screen
        Cursor.lockState = CursorLockMode.Locked;
        Cursor.visible = false;
    }

    private void Update()
    {
        //Get input from mouse
        _mouseX = Input.GetAxisRaw("Mouse X") * Time.fixedDeltaTime * xSensitivity;
        _mouseY = Input.GetAxisRaw("Mouse Y") * Time.fixedDeltaTime * ySensitivity;
        _yRotation += _mouseX;
        _xRotation -= _mouseY;
        //Clamp camera angle
        _xRotation = Mathf.Clamp(_xRotation, -cameraClampAngle, cameraClampAngle);
        //Apply rotations to camera and orientation
        transform.rotation = Quaternion.Euler(_xRotation, _yRotation, 0f);
        cameraOrientation.rotation = Quaternion.Euler(0f, _yRotation, 0f);
    }
}
```

Now go back into Unity and drag the `PlayerCamera` script onto the `Main Camera` object, assign the `Orientation` object to the `Camera Orientation` field int the script. You can now adjust the `Camera Clamp Angle` to your liking, this stops the camera from going past the given threshold when moving the `Main Camera` verticaly.

## 3. Create script to control the player.

### Script to control player movement.

In the scripts folder create a new script and call it `PlayerController`.

This script contols the movement of the `Player` with inputs from your keyboard:
```.cs
public class PlayerController : MonoBehaviour
{
    [Header("Movement Properties")] 
    public float movementSpeed = 70f;
    public float groundDrag = 5f;
    public float jumpForce = 12f;
    public float jumpCooldown = 0.25f;
    public float airMultiplier = 0.4f;
    private bool _readyToJump;

    [Header("Ground Check")] 
    public float playerHeight = 2f;
    public LayerMask groundLayer;
    private bool _isGrounded;

    [Header("Keybindings")] 
    public KeyCode jumpKey = KeyCode.Space;

    [Header("References")]
    public Transform orientation;

    private float _horizontalInput;
    private float _verticalInput;
    private Vector3 _moveDirection;
    private Rigidbody _playerRigidbody;

    private void Start()
    {
        //Find rigidbody on player and assign it to playerRigidbody value and lock the rotation
        _playerRigidbody = GetComponent<Rigidbody>();
        _playerRigidbody.freezeRotation = true;
        //Set the initial jump to be ready
        _readyToJump = true;
    }

    private void Update()
    {
        GroundCheck();
        GetInput();
        SpeedControl();
        HandleDrag();
    }

    private void FixedUpdate()
    {
        PlayerControl();
    }

    private void GetInput()
    {
        //Read values from keyboard and assign to horizontal and vertical values
        _horizontalInput = Input.GetAxisRaw("Horizontal");
        _verticalInput = Input.GetAxisRaw("Vertical");
        //Check for jump input
        if (Input.GetKey(jumpKey) && _readyToJump && _isGrounded)
        {
            _readyToJump = false;
            Jump();
            Invoke(nameof(ResetJump), jumpCooldown);
        }
    }

    private void PlayerControl()
    {
        //Get direction from orientation component
        _moveDirection = orientation.forward * _verticalInput + orientation.right * _horizontalInput;
        
        //Apply a movement force to the player
        if(_isGrounded)
            _playerRigidbody.AddForce(_moveDirection.normalized * movementSpeed, ForceMode.Force);
        else if(!_isGrounded)
            _playerRigidbody.AddForce(_moveDirection.normalized * movementSpeed * airMultiplier, ForceMode.Force);
    }
    
    private void GroundCheck()
    {
        //Shoot a raycast down from the player to check if the player is grounded
        _isGrounded = Physics.Raycast(transform.position, Vector3.down, playerHeight * 0.5f + 0.2f, groundLayer);
    }

    private void HandleDrag()
    {
        //If the player is grounded, set the drag levels to the groundDrag value otherwise set to 0
        _playerRigidbody.drag = _isGrounded ? groundDrag : 0f;
    }

    private void SpeedControl()
    {
        //Calculate the flat velocity of the player and if it exceds the movement speed then calculate the force to apply to 
        //limit the speed of the player
        Vector3 flatVelocity = new Vector3(_playerRigidbody.velocity.x, 0f, _playerRigidbody.velocity.z);
        if (flatVelocity.magnitude > movementSpeed)
        {
            Vector3 limitedVelocity = flatVelocity.normalized * movementSpeed;
            _playerRigidbody.velocity = new Vector3(limitedVelocity.x, _playerRigidbody.velocity.y, limitedVelocity.z);
        }
    }

    private void Jump()
    {
        //Handle jump mechanics
        //Reset y velocity to ensure the player jumps the same height each time
        _playerRigidbody.velocity = new Vector3(_playerRigidbody.velocity.x, 0f, _playerRigidbody.velocity.z);
        _playerRigidbody.AddForce(transform.up * jumpForce, ForceMode.Impulse);
    }

    private void ResetJump()
    {
        //This function resets the bool readyToJump
        _readyToJump = true;
    }
}
```

Now go back into Unity and drag the `PlayerController` script onto the `Player` object, assign the `Orientation` object to the `Orientation` field int the script. You can now adjust the values to your liking.

You can now test the scene yourself, or by running the demo scene provided.
