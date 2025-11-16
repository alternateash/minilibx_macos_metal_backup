# minilibx_macos_metal_backup
backup source for MiniLibX that works on macOS with metal for 42 projects

I didn't write this code!
I just uploaded the version of all the minilibx options and runarounds to get the fract-ol project to display on my m1 mac. 

I needed a way for my Makefiles to download and compile minilibx properly based on the system being compiled on, but I couldn't find a proper repo with all the necessary files for the mac version. So here we are.

I will update here with whatever legal / licence info for the original files

NOTE: For my Makefile to work properly, I had to include a line that copies libmlx.dylib to the root folder.
This is what that chunk of the Makefile looks like:

# OS-specific configuration
UNAME_S := $(shell uname -s)

ifeq ($(UNAME_S),Linux)
	LDFLAGS = -L libft -L $(MLX_DIR)
	LFLAGS = -lbsd -lXext -lX11 -lm
	MLX_LIB = $(MLX_DIR)/libmlx.a

else ifeq ($(UNAME_S),Darwin)
	LDFLAGS = -L libft -L $(MLX_DIR) -Wl,-rpath,@loader_path/minilibx
	LFLAGS = -framework Metal -framework AppKit -framework OpenGL -lm
	MLX_LIB = $(MLX_DIR)/libmlx.dylib
endif

all: $(NAME)

$(LIBFT):
	make -C ./libft

$(MLX_LIB):
ifeq ($(UNAME_S),Linux)
	git clone https://github.com/42Paris/minilibx-linux.git $(MLX_DIR)
else ifeq ($(UNAME_S),Darwin)
	git clone https://github.com/alternateash/minilibx_macos_metal_backup.git $(MLX_DIR)
endif
	make -C $(MLX_DIR)

%.o: %.c $(HEADERS)
	$(CC) $(CFLAGS) -c $< -o $@

$(NAME): $(HEADERS) $(OBJ) $(LIBFT) $(MLX_LIB)
	$(CC) $(CFLAGS) -o $(NAME) $(OBJ) $(LDFLAGS) $(LDLIBS) $(LFLAGS)
ifeq ($(UNAME_S),Darwin)
	@cp $(MLX_DIR)/libmlx.dylib .
endif
