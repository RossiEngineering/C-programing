#include <stdio.h>
#include <stdlib.h>
#include <SDL.h>
#define STB_IMAGE_IMPLEMENTATION
#include "stb_image.h"

int main(int argc, char* argv[]) {
    // SDL2 초기화
    if (SDL_Init(SDL_INIT_VIDEO) != 0) {
        printf("SDL_Init Error: %s\n", SDL_GetError());
        return 1;
    }

    // SDL 창 생성
    SDL_Window* window = SDL_CreateWindow("Image Viewer", SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, SDL_WINDOW_SHOWN);
    if (window == NULL) {
        printf("SDL_CreateWindow Error: %s\n", SDL_GetError());
        SDL_Quit();
        return 1;
    }

    // SDL 렌더러 생성
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_ACCELERATED);
    if (renderer == NULL) {
        printf("SDL_CreateRenderer Error: %s\n", SDL_GetError());
        SDL_DestroyWindow(window);
        SDL_Quit();
        return 1;
    }

    // 첫 번째 이미지(jcshim.jpg) 로딩
    const char* image_path1 = "jcshim.jpg";
    int width1, height1, channels1;
    unsigned char* img_data1 = stbi_load(image_path1, &width1, &height1, &channels1, 0);
    if (img_data1 == NULL) {
        printf("Image loading failed: %s\n", stbi_failure_reason());
        SDL_DestroyRenderer(renderer);
        SDL_DestroyWindow(window);
        SDL_Quit();
        return 1;
    }

    // 첫 번째 이미지(SDL_Surface로 변환)
    SDL_Surface* surface1 = SDL_CreateRGBSurfaceFrom(img_data1, width1, height1, channels1 * 8, width1 * channels1,
        0x0000FF, 0x00FF00, 0xFF0000, 0xFF000000);
    if (!surface1) {
        printf("SDL_CreateRGBSurfaceFrom Error: %s\n", SDL_GetError());
        stbi_image_free(img_data1);
        SDL_DestroyRenderer(renderer);
        SDL_DestroyWindow(window);
        SDL_Quit();
        return 1;
    }

    SDL_Texture* texture1 = SDL_CreateTextureFromSurface(renderer, surface1);
    SDL_FreeSurface(surface1);  // 텍스처로 변환 후 서페이스는 더 이상 필요 없음
    if (!texture1) {
        printf("SDL_CreateTextureFromSurface Error: %s\n", SDL_GetError());
        stbi_image_free(img_data1);
        SDL_DestroyRenderer(renderer);
        SDL_DestroyWindow(window);
        SDL_Quit();
        return 1;
    }

    // 두 번째 이미지(jcshim.png) 로딩
    const char* image_path2 = "jcshim.png";
    int width2, height2, channels2;
    unsigned char* img_data2 = stbi_load(image_path2, &width2, &height2, &channels2, 0);
    if (img_data2 == NULL) {
        printf("Image loading failed: %s\n", stbi_failure_reason());
        SDL_DestroyTexture(texture1);
        stbi_image_free(img_data1);
        SDL_DestroyRenderer(renderer);
        SDL_DestroyWindow(window);
        SDL_Quit();
        return 1;
    }

    // 두 번째 이미지(SDL_Surface로 변환)
    SDL_Surface* surface2 = SDL_CreateRGBSurfaceFrom(img_data2, width2, height2, channels2 * 8, width2 * channels2,
        0x0000FF, 0x00FF00, 0xFF0000, 0xFF000000);
    if (!surface2) {
        printf("SDL_CreateRGBSurfaceFrom Error: %s\n", SDL_GetError());
        stbi_image_free(img_data2);
        SDL_DestroyTexture(texture1);
        stbi_image_free(img_data1);
        SDL_DestroyRenderer(renderer);
        SDL_DestroyWindow(window);
        SDL_Quit();
        return 1;
    }

    SDL_Texture* texture2 = SDL_CreateTextureFromSurface(renderer, surface2);
    SDL_FreeSurface(surface2);  // 텍스처로 변환 후 서페이스는 더 이상 필요 없음
    if (!texture2) {
        printf("SDL_CreateTextureFromSurface Error: %s\n", SDL_GetError());
        stbi_image_free(img_data2);
        SDL_DestroyTexture(texture1);
        stbi_image_free(img_data1);
        SDL_DestroyRenderer(renderer);
        SDL_DestroyWindow(window);
        SDL_Quit();
        return 1;
    }

    // 이미지 렌더링 (두 개의 이미지를 나란히 출력)
    SDL_RenderClear(renderer);

    // 첫 번째 이미지를 왼쪽에 출력
    SDL_Rect dest_rect1 = { 0, 0, width1, height1 };  // 첫 번째 이미지 위치와 크기
    SDL_RenderCopy(renderer, texture1, NULL, &dest_rect1);

    // 두 번째 이미지를 오른쪽에 출력
    SDL_Rect dest_rect2 = { width1, 0, width2, height2 };  // 두 번째 이미지 위치와 크기
    SDL_RenderCopy(renderer, texture2, NULL, &dest_rect2);

    // 렌더링된 이미지를 화면에 표시
    SDL_RenderPresent(renderer);

    // 이벤트 루프
    SDL_Event e;
    while (1) {
        if (SDL_PollEvent(&e) && e.type == SDL_QUIT) {
            break;
        }
    }

    // 메모리 해제
    SDL_DestroyTexture(texture1);
    SDL_DestroyTexture(texture2);
    stbi_image_free(img_data1);
    stbi_image_free(img_data2);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();

    return 0;
}
