def vacuum_cleaner(rooms):
    print("Initial Room Status:")
    print(rooms)

    # Traverse through each room
    for i in range(len(rooms)):
        if rooms[i] == 0:
            print(f"Room {i+1} is Dirty. Cleaning...")
            rooms[i] = 1
        else:
            print(f"Room {i+1} is already Clean.")

    print("\nFinal Room Status:")
    print(rooms)


# 0 = Dirty, 1 = Clean
# Example: [0, 1, 0, 1] means Room1=Dirty, Room2=Clean, Room3=Dirty, Room4=Clean
rooms = [0, 1, 0, 1]

vacuum_cleaner(rooms)
