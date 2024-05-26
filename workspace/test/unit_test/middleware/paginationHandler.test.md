import  paginationHandler  from '../../../src/common/middlewares/paginationHandler'

export const paginationTest = () => {
  describe('test paginationHandler functions', () => {
    const mockRequest = {
        query: {
          page: 1,
          limit: 1,
        },
      },
      mockResponse = {},
      next = jest.fn()

    test('prepares pagination elements for indexing data from DB', () => {
      paginationHandler(mockRequest, mockResponse, next)

      expect(typeof mockRequest).toBe('object')
      expect(mockRequest.query.limit).toEqual(1)
      expect(mockRequest.query.offset).toEqual(0)
      expect(next).toHaveBeenCalled()
    })

    test('prepares pagination elements for indexing data from DB', () => {
      paginationHandler(mockRequest, mockResponse, next)

      expect(mockRequest.query.limit).not.toEqual(2)
      expect(mockRequest.query.offset).not.toEqual(1)
      expect(next).toHaveBeenCalled()
    })
  })
}
